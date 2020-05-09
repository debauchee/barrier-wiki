<a name="top"></a>

# Barrier Troubleshooting Steps

This guide has steps that can be tried for troubleshooting Barrier. These steps can help isolate the issue and recommend things to try to resolve the issue. Specific steps for making configuration changes outside of Barrier (i.e. Network, Firewall, Permissions, etc...) are outside the scope of this document and the appropriate references should be used.

## Document Conventions

- Text in angle brackets `<text>` is input supplied by the user
- Text in _italics_ are user interface elements, items on-screen, or filenames
- Text in a `monospaced font` are keyboard shortcuts, prompts, commands, or command output

## Things to Check


- [The Command Line](#command_line)
- [Processes Running](#processes_running)
- [Server Reachability](#server_reachability)
- [Server Listening](#server_listening)
- [Multiple Instances](#multiple_instances)
- [Fingerprint Trust](#fingerprint_trust)


## <a name="command_line">Command Line</a>
Most of the tools used to troubleshoot in this guide are run from a text-based command line. These are some possible steps to get to the command line interface.

__Note:__ None of the below commands should require elevated access (`Administrator cmd.exe`, `sudo`, `su`, `doas`, or equivalent) unless specified.

### Windows Command Prompt
1. Press the `(⊞ Win) + R` to open the _Run_ dialog
2. In the _Open:_ textbox, type `cmd` and click _OK_

### Windows PowerShell
1. Press the `(⊞ Win) + R` to open the _Run_ dialog
2. In the _Open:_ textbox, type `powershell` and click _OK_

### MacOS Terminal.app
1. Press `(⌘ Cmd) + Space` to open _Spotlight_ search
2. Type `Terminal.app` and press `Enter`

### Linux Virtual Terminal
There are many ways to open a terminal emulator in the GUI on various linux distributions. If you do not know how to access a terminal emulator (`xterm`, `konsole`, `gnome-terminal`, etc...) the steps for accessing the console are fairly standard. Press `Ctrl + Alt` and one of the Function keys (usually `F1` through `F7`) to switch between back and forth between the GUI and virtual terminals. 

[Back to Top](#top)

## <a name="processes_running">Processes Running</a>
Barrier uses multiple process to create the software KVM effect. If using the GUI, check that GUI icon is in the _system tray_ (Windows or some Linux desktops) or on the right side of the _menu bar_ (MacOS). The GUI is only used to configure and control the client or server processes. 

### Windows Processes
- Press `Ctrl + Shift + Esc` to open the _Task Manager_
- Click on the _Details_ tab and click on the _Name_ header to sort by name
- Look for the `barrier.exe` process if the GUI is running
- Look for the `barrierd.exe` process
- Look for the `barrierc.exe` process on a client
- Look for the `barriers.exe` process on a server

### Linux/Unix/MacOS
- Use [ps](https://en.wikipedia.org/wiki/Ps_(Unix)) and [grep](https://en.wikipedia.org/wiki/Grep) commands to find barrier processes:
```shell
ps -ax -o user,pid,command | grep -i [b]arrier
```
- Look for the `barrier` process if the GUI is running
- Look for the `barrierc` process on a client
- Look for the `barriers` process on a server

__Note:__ Including the brackets [] in the `grep` syntax prevents it from matching instances of `grep` in the process list.

[Back to Top](#top)

## <a name="server_reachability">Server Reachability (Ping)</a>
Barrier needs network connectivity between machines to work. The easiest way to test this is with the [ping](https://en.wikipedia.org/wiki/Ping_(networking_utility)) or [Test-Connection](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/test-connection) commands.

### Windows Command Prompt
```cmd
ping <hostname or IP>
```

### Windows PowerShell
```powershell
Test-Connection -ComputerName <hostname or IP>
```

### Linux/Unix/MacOS Command Line
```shell
ping -c 4 <hostname or IP>
```

__If successful you should see four replies with the IP address and the time (in milliseconds)__

__Steps to try if unsuccessful:__
- Make sure that both devices are either on the same Local Area Network (LAN) or check that the LANs they are on are connected and routing
- Make sure that ping (ICMP) is being allowed through any software firewalls or hardware firewalls between the devices. (This can be turned back off after troubleshooting is resolved)
- If using a hostname, make sure you are able to resolve that hostname to an IP address (Local DNS Server, mDNS, Avahi, NETBIOS, `/etc/hosts`, or equivalent method). Alternatively, consider using IP addresses instead

[Back to Top](#top)

## <a name="server_listening">Server Listening (Port 24800)</a>
The server listens for a network connection on port 24800. You can test connectivity to this port from both the server and its clients. Check connectivity from the server to itself first to make sure it is accepting connections, then check that you can connect to the server across the network from the client. 

- Make sure the server has been started in the Barrier GUI or that the required [processes are running](#processes_running) first

### Windows PowerShell Test-NetConnection
- In the PowerShell window run:
```powershell
Import-Module NetTCPIP
Test-NetConnection -ComputerName <hostname or IP> -Port 24800
```
- If you see `TcpTestSucceeded : True` you are able to connect to the server

__Note:__ If you know of an __easy__ way to test SSL connectivity with Windows please feel free to contribute it here. This method only tests connectivity to the port.

### Windows OpenSSL Client Connect
The `openssl` command used below for Linux/Unix/MacOS to test connectivity __and__ SSL encryption can be obtained for Windows from various third parties distributions. Check the [OpenSSL Wiki](https://wiki.openssl.org/index.php/Binaries) for more information.

### Linux/Unix/MacOS OpenSSL Client Connect
- Use the [openssl](https://wiki.openssl.org/index.php/Command_Line_Utilities) command in the terminal to connect to the server by hostname:
```shell
$ openssl s_client -connect <hostname or ip>:24800 | head
```
- If you see `CONNECTED` you are able to connect to the server
- If you see `Server Certificate` the SSL encryption is working
- You may see `verify error:num=18:self signed certificate`. Barrier generates a self-signed certificate, so this is nothing to be concerned about

__Steps to try if unsuccessful:__
- Make sure the Barrier server [process is runnning](#processes_running)
- Make sure that port `24800` inbound is being allowed through any software firewalls or hardware firewalls between the devices
- Make sure that port `24800` outbound is being allowed through any software firewalls on the client
- Instructions for adding Barrier to the Windows firewall can be found [here](Adding-Barrier-to-the-Windows-Firewall.md)

[Back to Top](#top)

## <a name="multiple_instances">Multiple Instances</a>
Multiple instances of the background services that Barrier uses (`barriers` on the server, and `barrierc` on the client) can cause issues. For example, if another `barriers` process is running in the backround it will prevent the current process from binding to port `24800` and you may see incorrect status in the GUI.

### Windows
- Press `Ctrl + Shift + Esc` to open the _Task Manager_
- Click on the _Details_ tab and click on the _Name_ header to sort by name
- Make sure there is only one `barrier.exe` process if the GUI is running
- Make sure there is only one `barriers.exe` and one `barrierd.exe` process if the server is running
- Make sure there is only one `barrierc.exe` and one `barrierd.exe` process if the client is running

### Linux/Unix/MacOS
- Use `grep` and `ps` to find barrier processes
```shell
ps -ax -o user,pid,command | grep -i [b]arrier
```
- Make sure there is only one `barrier` process if the GUI is running
- Make sure there is only one `barriers` process if the server is running
- Make sure there is only one `barrierc` process if the client is running

__Note:__ The barrier command line arguments may make a single command wrap on the terminal to more than one line

__Steps to try if unsuccessful:__
- Try stopping all of the barrier processes (_End Task_ on Windows, `kill <pid>` on Linux/Unix/MacOS) and relaunching the program
- Try restarting the computer

[Back to Top](#top)

## <a name="fingerprint_trust">Fingerprint Trust</a>
Barrier uses fingerprints to validate that a malicious server is not trying to intercept a client connection. These fingerprints may be automatically generated if _Auto Config_ is selected in the client. If your client does not trust the fingerprint it will refuse the connection.

The _TrustedServers.txt_ file should contain the fingerprint found in the _Local.txt_ file on the server.

### View the server fingerprint in the GUI
- Open the main Barrier GUI window
- The _SSL Fingerprint:_ field in the _Server_ section shows the current server fingerprint

### View the fingerprints on Windows
- Open _File Explorer_
- In the _path bar_ enter `%LocalAppData%\Barrier\SSL\Fingerprints`
- Double-click either _Local.txt_ for the server fingerprints, or _TrustedServers.txt_ to view the fingerprints trusted by the client

### View the fingerprints on MacOS
- To view the trusted fingerprints on the client use the [more](https://en.wikipedia.org/wiki/More_(command)) command to view the _TrustedServers.txt_ file:
```shell
more ~/Library/Application\ Support/barrier/SSL/Fingerprints/TrustedServers.txt
```
- To view the server fingerprint on the server use the `more` command again:
```
more ~/Library/Application\ Support/barrier/SSL/Fingerprints/Local.txt
```

### View the trusted fingerprints on Linux or Unix
- To view the trusted fingerprints on the client use the [more](https://en.wikipedia.org/wiki/More_(command)) command to view the _TrustedServers.txt_ file:
```shell
more ~/.local/share/barrier/SSL/Fingerprints/TrustedServers.txt
```
- To view the server fingerprint on the server use the `more` command again:
```shell
more ~/.local/share/barrier/SSL/Fingerprints/Local.txt
```

On Linux or Unix systems the barrier _TrustedServers.txt_ or _Local.txt_ files may not always be in the same place depending on your system configuration. If they are elsewhere, use the [find](https://en.wikipedia.org/wiki/Find_(Unix)) command to locate them.
```
find / -type f -name '*TrustedServers.txt' 2>/dev/null
find / -type f -name '*Local.txt' 2>/dev/null
```
__Note 1:__ These `find` commands may find other files with the same names. Check that the path includes `barrier`

__Note 2:__ The `2>/dev/null` is to hide _Permission denied_ errors you would otherwise get from searching directories you do not have permission for as a normal user

__Steps to try if unsuccessful:__
- Try to copy & paste the contents of _Local.txt_ on the server into the _TrustedServers.txt_ file manually

[Back to Top](#top)
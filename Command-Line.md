<a name="top"></a>

# Using Barrier from the Command Line

You might to use the command line without the GUI to run barrier, especially if
you do not change settings frequently or do not want the Qt GUI running all the
time. These instructions cover using Barrier via the command line interface.

_The [Synergy wiki](https://github.com/symless/synergy-core/wiki) was
used as a reference in creating this documentation._

## Document Conventions

- Text in angle brackets `<text>` is input supplied by the user
- Text in square brackets `[text]` represents optional inputs
- Text in curly braces with pipes `{true|false}` represents options to choose
from
- Text in _italics_ are user interface elements or items on-screen
- Text in a `monospaced font` are keyboard shortcuts, prompts, commands, or
command output

## Topics

- [Windows](#windows)
- [MacOS](#macos)
- [Linux](#linux)
- [Server Command Line Options](#server_cli)
- [Client Command Line Options](#client_cli)
- [Systemd Service](#systemd)
- [Launchd Service](#launchd)
- [Windows Service](#windows_service)
- [Text File Configuration](#text_config)
- [Server Configuration File](#server_config)
- [SSL/TLS Configuration](#ssl_config)

---

## <a name="windows">Windows</a>

To use Barrier commands from the command line in Windows you can either change
directories to the folder that they are located in each time or add the Barrier
directory to your `%PATH%` variable.

To change to the directory:

```cmd
cd "\Program Files\Barrier"
```

To add the directory to `%PATH%`:

- Press `(⊞ Win) + Break` to view the _System_ control panel
- Click on _Advanced system settings_ on the left panel
- On the _Advanced_ tab, click the _Environment Variables..._ button
- In the _System variables_ list, double-click the _Variable_ named _Path_
- Click _New_ and enter `C:\Program Files\Barrier`
- Click _OK_ in the _Edit environment variable_ dialog
- Click _OK_ in the _Environment Variables_ dialog
- Click _OK_ in the _System Properties_ dialog

This will only effect command prompts opened after the change.

### Portable

The command line version of Barrier is a single client executable `barrierc.exe`
and a single server executable `barriers.exe`. They both have a dependency to OpenSSL
libraries, `libeay32.dll` and `ssleay32.dll` (used for encryption with argument
`--enable-crypto`), as well as Microsoft Visual C++ runtime libraries.

From an existing installation you can copy the necessary program files to
a location of choice, to get a command line only portable (depending on configuration)
installation. Copy the following files from the installation directory `C:\Program Files\Barrier`:

```
barrierc.exe
barriers.exe
libeay32.dll
ssleay32.dll
```

To be able to generate server certificate, you can also choose to include the OpenSSL
application itself (on the server), together with Barrier's predefined OpenSSL
configuration file:

```
openssl.exe
barrier.conf
```

As long as you have the [Microsoft Visual C++ Redistributable for Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) installed (or copy the necessary runtime libaries
`msvcp140.dll`, `vcruntime140.dll` and `vcruntime140_1.dll` into the application directory),
you will now have a stand-alone application directory that you can manually copy into computers
where you need it.

For a completely portable installation, with local configuration, you must configure the
location of server configuration file and SSL/TLS configuration files. See [Text File Configuration](#text_config), [Server Command Line Options](#server_cli),
[Client Command Line Options](#client_cli) and [SSL/TLS Configuration](#ssl_config), below.

<a href="#top">Back to top</a>

---

## <a name='macos'>MacOS</a>

To use Barrier commands from the command line on MacOS you can either change
directories to the folder they are located and run them using the relative path
(i.e. `./barriers`) each time or add the `Barrier.app/Contents/MacOS` directory 
to your `$PATH`.

```cmd
cd /Applications/Barrier.app/Contents/MacOS/
```

To add the directory to `$PATH`:
```cmd
export PATH=/Applications/Barrier.app/Contents/MacOS/:$PATH
```

The `$PATH` variable will only be set for the duration of the current shell
session. To make the path persistent, add it as a new line in the `/etc/paths`
file or set the `$PATH` variable in your `.profile` or `.bashrc` file.

<a href="#top">Back to top</a>

---

## <a name="linux">Linux/Unix</a>

If Barrier has been installed using a package manager or equivalent it should
already be in your `$PATH`.

If installed using flatpak, you can run the command line client like this:
```cmd
flatpak run --command=barrierc com.github.debauchee.barrier
```
And server like this:
```cmd
flatpak run --command=barriers com.github.debauchee.barrier
```

<a href="#top">Back to top</a>

---

## <a name="server_cli">Server Command Line Options</a>

To see the server command line options, use the `--help` argument:
```
barriers --help
Start the barrier server component.

Usage: barriers [--address <address>] [--config <pathname>] [--daemon|--no-daemon] [--name <screen-name>] [--restart|--no-restart] [--debug <level>]

Options:
  -a, --address <address>  listen for clients on the given address.
  -c, --config <pathname>  use the named configuration file instead.
  -d, --debug <level>      filter out log messages with priority below level.
                             level may be: FATAL, ERROR, WARNING, NOTE, INFO,
                             DEBUG, DEBUG1, DEBUG2.
  -n, --name <screen-name> use screen-name instead the hostname to identify
                             this screen in the configuration.
  -1, --no-restart         do not try to restart on failure.
      --restart            restart the server automatically if it fails. (*)
  -l  --log <file>         write log messages to file.
      --no-tray            disable the system tray icon.
      --enable-drag-drop   enable file drag & drop.
      --enable-crypto      enable the crypto (ssl) plugin.
      --profile-dir <path> use named profile directory instead.
  -f, --no-daemon          run in the foreground.
```

<a href="#top">Back to top</a>

---

## <a name="client_cli">Client Command Line Options</a>

To see the client command line options, use the `--help` argument:
```
barrierc --help
Start the barrier client and connect to a remote server component.

Usage: barrierc [--yscroll <delta>] [--daemon|--no-daemon] [--name <screen-name>] [--restart|--no-restart] [--debug <level>] <server-address>

Options:
  -d, --debug <level>      filter out log messages with priority below level.
                             level may be: FATAL, ERROR, WARNING, NOTE, INFO,
                             DEBUG, DEBUG1, DEBUG2.
  -n, --name <screen-name> use screen-name instead the hostname to identify
                             this screen in the configuration.
  -1, --no-restart         do not try to restart on failure.
      --restart            restart the server automatically if it fails. (*)
  -l  --log <file>         write log messages to file.
      --no-tray            disable the system tray icon.
      --enable-drag-drop   enable file drag & drop.
      --enable-crypto      enable the crypto (ssl) plugin.
      --profile-dir <path> use named profile directory instead.
  -f, --no-daemon          run in the foreground.
      --daemon             run as a daemon. (*)
```

<a href="#top">Back to top</a>

---

## <a name="systemd">Creating a systemd service (Linux)</a>

[comment]: <> (TODO: This section will need to be updated if PR #694 is merged)

If you would like to add the barrier client or server as a service on a 
systemd-based linux distribution, you can create a 
[`.service` file for systemd](https://www.freedesktop.org/software/systemd/man/systemd.service.html).

Running Barrier as a systemd service as the root user will allow Barrier to
access any X11 screen. If you need to use Barrier before logging (i.e. in a
Display Manager like GDM) in this is possibly the only option despite the
possible security concern of running Barrier as root. If you do not need to use
Barrier at the login screen it is likely better to run Barrier under your user
account.

Here is an example `.service` template for a service on a computer used by one
person where Barrier does not need access to the Display Manager. This can be
put in the `/etc/systemd/system/` directory and started or stopped with the
`systemctl` command. This assumes that the X11 display is on `:0`.

```ini
[Unit]
Description=Barrier Client daemon
After=network.target

[Service]
User=<username>
Group=<groupname>
ExecStart=barrierc --enable-crypto --display :0 --debug INFO -f <server hostname or IP>
Restart=always

[Install]
WantedBy=multi-user.target
```

If you want to use a systemd Barrier service on a multi-user system, consider 
using a [user service](https://www.freedesktop.org/software/systemd/man/user@.service.html)
or starting the GUI application at logon instead.

The barrier client can also be started from the command line remotely using
`ssh` as long as `$DISPLAY` is set or specified with `--display`.

<a href="#top">Back to top</a>

---

## <a name="launchd">Launchd daemon (MacOS)</a>

If you want to create a daemon for MacOS instead of using the GUI, you can 
create a daemon/agent using a `.plist` file in `~/Library/LaunchAgents`. 
Information about creating daemons/agents for MacOS can be found at 
[launchd.info](https://www.launchd.info/).

The recommended method of starting automatically on MacOS is with the GUI by
dragging _Barrier.app_ to the _Login Items_ pane in _System Preferences_ >
_Users & Groups_. This method does start Barrier after logging in.

<a href="#top">Back to top</a>

---

## <a name="windows_service">Windows Service</a>

The Windows version uses a service that can be started/stopped in the Windows
[Services snap-in](https://en.wikipedia.org/wiki/Windows_service#Services_snap-in).
The Services snap-in can be accessed by pressing `(⊞ Win) + R` and typing
`services.msc` in the _Run_ dialog. The service is named `Barrier`.

<a href="#top">Back to top</a>

---

## <a name="text_config">Text File Configuration</a>

If you use Barrier from the command line you may need to create a configuration
file. Configuration files can be copied from their default GUI locations as a
baseline.

The client will use the configuration specified by the server.

By default the server will look for a configuration file at the following paths, in prioritized order:
- User specific location: `%LocalAppData%\Barrier\barrier.sgc` on Windows, `~/.local/share/barrier/.barrier.conf` or `$XDG_DATA_HOME/barrier/.barrier.conf` on Linux.
- System shared location: `C:\ProgramData\Barrier\barrier.sgc` on Windows, `/etc/barrier.conf` on Linux.

The user specific location can be customized with command line argument `--profile-dir`,
and Barrier will look for a configuration file with default name (`barrier.sgc` on Windows,
`.barrier.conf` on Linux) there:

```shell
barriers --profile-dir ~/barrier/config/path ...
```

You can also use command line argument `--config` to set path to a specific configuration
file that should be used.

```shell
barriers --config ~/barrier/config/path/file.conf ...
```

<a href="#top">Back to top</a>

---

## <a name="server_config">Server Configuration File</a>

The text file configuration uses four different sections; `screens`, `aliases`,
`links` and `options`. The structure of a server configuration file looks
something like this:

```yaml
section: screens
    hostname1:
        setting = value
    hostname2:
        setting = value
end
section: aliases
    hostname1:
        hostname1.alias
    hostname2:
        hostname2.alias
end
section: links
    hostname1:
        direction = hostname2
    hostname2:
        direction = hostname1
end
section: options
    option = value
end
```

Examples can be found [in the `doc` directory](https://github.com/debauchee/barrier/tree/master/doc) of the Barrier repository.

<a href="#top">Back to top</a>

---

### Screen Options

By default screens use the hostname of the server or client. Each screen may
have any of the following settings:
```ini
halfDuplexCapsLock = {true|false}
halfDuplexNumLock = {true|false}
halfDuplexScrollLock = {true|false}
switchCorners = {none, top-left, top-right, bottom-left, bottom-right, left, right, top, bottom, all}
switchCornerSize = N
xtestIsXineramaUnaware = {true|false}
preserveFocus = {true|false}
shift = {shift|ctrl|alt|meta|super|none}
ctrl = {shift|ctrl|alt|meta|super|none}
alt = {shift|ctrl|alt|meta|super|none}
altgr = {shift|ctrl|alt|meta|super|none}
meta = {shift|ctrl|alt|meta|super|none}
super = {shift|ctrl|alt|meta|super|none}
```
- `halfDuplexCapsLock`, `halfDuplexNumLock`, and `halfDuplexScrollLock` are for
clients that use a press to enable and a release to disable instead of toggling
with a press and release
- `switchCorners` prevents switching when reaching the edge of any of the listed
corners
- `switchCornerSize` is the size in pixels to ignore on a screen edge when using
`switchCorners`
- `xtestIsXineramaUnaware` works around a bug when using certain versions of the
XTest extension in combination with Xinerama on X11 clients (Linux)
- `preserveFocus` prevents dropping focus of the current window when switching
screens
- `shift, ctrl, alt, altgr, meta,` and `super` allow these keys to be mapped to
a different key for that screen

<a href="#top">Back to top</a>

---

### Aliases

By default a screen name is the hostname of the client or server. However, the
screen name can be specified on the command line for both the server and client
with the `--screen` option and if there is a matching Alias it will be 
recognized. Aliases must be unique.

<a href="#top">Back to top</a>

---

### Links

The links section describes the screens relationships to each other. Links allow
you to specify how screens are layed in relation to each other. Links use the
following format:
```
{left|right|up|down}(<range>) = name(<range>)
```

Each link defines a screen edge and the screen name it connects to. The range is
an optional percentage (between 0 and 100) of the edge that will connect to the
other screen. For example, if there are two hosts on the right of `host1` and
they are split 50-50 on the right edge, the configuration might look something
like this:

```yaml
section: links
    host1:
        right(0,50) = host2(50,100)
        right(50,100) = host3(0,50)
...
```

Overlaps on the screen ranges are not supported.

A simpler configuration with two hosts might look like this:

```yaml
section: links
    host1:
        right = host2
    host2:
        left = host1
```

The GUI can be used to set up the layout and the configuration can be copied
into the configuration file being used for the command line.

<a href="#top">Back to top</a>

---

### Options

The key/value pairs in the options section are global options that apply to all
screens.
```ini
heartbeat = N
switchCorners = {none, top-left, top-right, bottom-left, bottom-right, left, right, top, bottom, all}
switchCornerSize = N
switchDelay = N
switchDoubleTap = N
screenSaverSync = {true|false}
relativeMouseMoves = {true|false}
clipboardSharing = {true|false}
clipboardSharingSize = N
win32KeepForeground = {true|false}
keystroke(key) = actions
mousebutton(button) = actions
```

- `heartbeat` disconnects clients if they do not send a message within `N` 
milliseconds
- `switchCorners` prevents switching screens when reaching the edge on any
of the listed corners
- `switchCornerSize` is the size in pixels to ignore on a screen edge when using
`switchCorners`
- `switchDelay` prevents switching unless the mouse rests on the edge for 
`N` milliseconds
- `switchDoubleTap` prevents switching unless the edge is double-tapped 
within `N` milliseconds
- `screenSaverSync` will sync the screensavers if set to `true`. If set to false
the clients will use their own screensaver settings and the server screensaver
won't start as long as there is input to any screen
- `relativeMouseMoves` causes clients to move the mouse using relative 
coordinates rather than absolute when the cursor is locked to a screen. This is
may assist with certain games
- `clipboardSharing` enables sharing clipboard contents between server and
clients if set to `true`
- `clipboardSharingSize` sets a limit of `N` Kb of data when clipboard sharing
is enabled
- `win32KeepForeground` grabs the focus of on a Windows server on switching to a
client if set to `true`. If set to `false` it will leave the current window in
the foreground. Leaving this setting on `true` avoids issues with other apps
interfering with reading hardware inputs
- `keystroke` can be used to bind <a href="#actions">actions</a> to 
<a href="#keys_list">keys</a>
- `mousebutton` can be used to bind a modifier and a mouse button (left is 1,
middle is 2, and right is 3) to an <a href="#actions">action</a>.

<a href="#top">Back to top</a>

---

### <a name="actions">Actions</a>

<a href="#top">Back to top</a>

---

### <a name="keys_list">Keys</a>

Keys can be in either unicode hexadecimal format (i.e. `\uXXXX`) or a valid key
name. The following named keys are valid in a configuration:

| Standard Keys | Standard Keys | Keypad Keys    | Media Keys | Web Keys     |
|---------------|---------------|----------------|------------|--------------|
| F1 to F35     | Left          | KP_0 to KP_9   | AppMail    | WWWBack      |
| Ampersand     | LeftTab       | KP_F1 to KP_F4 | AppMedia   | WWWFavorites |
| Apostrophe    | Less          | KP_Add         | AppUser1   | WWWForward   |
| Asterisk      | Linefeed      | KP_Begin       | AppUser2   | WWWHome      |
| At            | Menu          | KP_Decimal     | AudioDown  | WWWRefresh   |
| BackSpace     | Minus         | KP_Delete      | AudioMute  | WWWSearch    |
| Backslash     | NumLock       | KP_Divide      | AudioNext  | WWWStop      |
| Bar           | Number        | KP_Down        | AudioPlay  |              |
| Begin         | PageDown      | KP_End         | AudioPrev  |              |
| BraceL        | PageUp        | KP_Enter       | AudioStop  |              |
| BraceR        | ParenthesisL  | KP_Equal       | AudioUp    |              |
| BracketL      | ParenthesisR  | KP_Home        | Eject      |              |
| BracketR      | Pause         | KP_Insert      |            |              |
| Break         | Percent       | KP_Left        |            |              |
| Cancel        | Period        | KP_Multiply    |            |              |
| CapsLock      | Plus          | KP_PageDown    |            |              |
| Circumflex    | Print         | KP_PageUp      |            |              |
| Clear         | Question      | KP_Right       |            |              |
| Colon         | Redo          | KP_Separator   |            |              |
| Comma         | Return        | KP_Space       |            |              |
| Delete        | Right         | KP_Subtract    |            |              |
| Dollar        | ScrollLock    | KP_Tab         |            |              |
| DoubleQuote   | Select        | KP_Up          |            |              |
| Down          | Semicolon     |                |            |              |
| End           | Slash         |                |            |              |
| Equal         | Sleep         |                |            |              |
| Escape        | Space         |                |            |              |
| Exclaim       | Space         |                |            |              |
| Execute       | SysReq        |                |            |              |
| Find          | Tab           |                |            |              |
| Grave         | Tilde         |                |            |              |
| Greater       | Underscore    |                |            |              |
| Help          | Undo          |                |            |              |
| Home          | Up            |                |            |              |
| Insert        |               |                |            |              |

<a href="#top">Back to top</a>

----

## <a name="client_config">Client Configuration File</a>

Client options are set either by the command line arguments or by the server the
client has connected to. There is no `--config` argument for the `barrierc`
client command.

The client saves a configuration file that can be read for informational or
troubleshooting purposes, and is structured like an `.ini` file. It has two
sections, `[General]` and `[internalConfig]`.

<a href="#top">Back to top</a>

## <a name="ssl_config">SSL/TLS Configuration</a>

Barrier supports SSL/TLS encryption, by use of the `OpenSSL` library (included).
This must be anabled with command line argument `--enable-crypto`, and requires a
certificate and fingerprint to be configured.

The SSL related configuration is kept in subdirectory "SSL" in the same user specific location
as the [text file configuration](#text_config) is loaded from: By default
`%LocalAppData%\Barrier\SSL` on Windows, `~/.local/share/barrier/SSL` or `$XDG_DATA_HOME/barrier/SSL`
on Linux, but configurable with command line argument `--profile-dir`.

On the server, the root of the SSL directory must contain the certificate as a file
with name `Barrier.pem`, containing the private and public key.

Barrier uses fingerprints to validate that a malicious server is not trying to intercept a client
connection. A server's fingerprint must be generated from the certificate, and may be kept
in file `SSL/Fingerprints/Local.txt` on the server. All clients must have the fingerprint
hash string of trusted servers in a file `SSL/Fingerprints/TrustedServers.txt`.
When connecting to a server, if it presents a fingerprint not explicitely trusted by the client,
it will refuse the connection. See also [Fingerprint trust troubleshooting](https://github.com/debauchee/barrier/wiki/Troubleshooting#fingerprint-trust).

The server will therefore typically contain the following files:
```
/SSL/Barrier.pem
/SSL/Fingerprints/Local.txt
```

Clients must contain the following file:
```
/SSL/Fingerprints/TrustedServers.txt
```

### Generating certificate and fingerprint

The main UI application has built-in functionality for handling encryption.
In server mode it will generate a self-signed server certificate and a fingerprint.
In client mode it will prompt for you to accept the server's fingerprint, and add
it to your list of trusted servers.
In a command line only ([portable](#portable)) environment you will have to handle
this manually. You can use the OpenSSL command line utility which is included in
a Barrier installation together with a Barrier specific OpenSSL configuration
file `barrier.conf`. To create them the same way as the UI application does,
you can follow the following Windows example. It uses `openssl.exe` and `barrier.conf`
from a Barrier installed in default location `C:\Program Files\Barrier`, generating
configuration in default location `%LocalAppData%\Barrier\SSL`. If you have the
OpenSSL files in a different location and/or are planning to keep the SSL files in
a custom location specified with command line argument `--profile-dir`, you must
change the paths in the example accordingly.

```
MKDIR "%LocalAppData%\Barrier\SSL\Fingerprints" >NUL 2>&1
SET OPENSSL_CONF=C:\Program Files\Barrier\barrier.conf
SET RANDFILE=%LocalAppData%\Barrier\SSL\.rnd
"C:\Program Files\Barrier\openssl.exe" req -x509 -nodes -days 365 -subj /CN=Barrier -newkey rsa:2048 -keyout "%LocalAppData%\Barrier\SSL\Barrier.pem" -out "%LocalAppData%\Barrier\SSL\Barrier.pem"
IF EXIST "%RANDFILE%" DEL "%RANDFILE%"
FOR /F "tokens=2 delims=^=" %a in ('""C:\Program Files\Barrier\openssl.exe" x509 -fingerprint -sha1 -noout -in "%LocalAppData%\Barrier\SSL\Barrier.pem""') DO ECHO %a > "%LocalAppData%\Barrier\SSL\Fingerprints\Local.txt"
```

Now, on any clients you must manually ensure there is a text file
`%LocalAppData%\Barrier\SSL\Fingerprints\TrustedServers.txt`,
and append a line to it, with the hash string from the server's
`%LocalAppData%\Barrier\SSL\Fingerprints\Local.txt`,
e.g.

```
96:32:AB:DD:38:5C:E5:21:20:8E:52:E8:83:28:A0:2A:CC:CC:8F:A3
```



---
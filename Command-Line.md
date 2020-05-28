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

When a text file configuration is used it needs to be specified with the
`--config` option on the command line or it will use the default locations.

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

---
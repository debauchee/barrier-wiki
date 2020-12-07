# Immune Keys

## Overview

### What are Immune Keys?
Immune keys are keyboard keys that Barrier will not forward to other PCs. They are "immune" from being blocked on the host (server).

### Why would I want them?
You probably don't. Barrier works best when your keyboard and mouse function as if they were physically attached to every connected client. There are situations, however, in which a user might not want a certain key or keys forwarded. In this case you would simply list them as immune keys and they will always pass directly to the host machine and never to a client.

### What are the requirements?
At this time Immune Keys are a Windows-only feature; your keyboard must be connected to a Windows machine.

## Immune Keys Procedure

- Check [this table](https://msdn.microsoft.com/en-us/library/windows/desktop/dd375731(v=vs.85).aspx) for the virtual key code value of the key you want to immune.
- Create or open ImmuneKeys.txt inside your profile directory, %LocalAppData%\Barrier by default, configurable with `--profile-dir` when running from [command line](Command-Line.md).
- Add one line per key to this file, save it, and close it.
  - Each line starts with a keycode in decimal or hexadecimal and can follow with human-readable text to use as a comment field.
  - Any line that starts with a # is considered a comment line and is ignored. Blank lines are also ignored.
- *Optional:* Depending on your use-case it may be necessary to prevent the server from elevating so that non-elevated server-side application will still be able to receive immune key notification while the client has focus. To do so change the Elevate option in settings to Never before restarting the host server in the step below.
- Restart host server from the Barrier GUI (click Stop, then Start) to effect your changes.

# Example Configuration

    # this is a comment line. describe the reason for the following immune key here

    # prevent Escape key from being forwarded to clients 
    0x1B

    # you can also add comments after the keycode column
    # everything after the keycode is ignored so no # is required

    163 VK_RCONTROL (0xA3)	for discord muting

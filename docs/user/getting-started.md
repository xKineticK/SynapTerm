# Getting started

## Install

SynapTerm is in closed beta: ask for access at [synapterm.com](https://synapterm.com). The invitation email takes you to a page with your license key and the installers. To activate, open *Settings → License*, paste the key and press *Activate* (it works on up to 3 devices).

| System | File | Notes |
| --- | --- | --- |
| Windows 10/11 | `SynapTerm_x.y.z_x64-setup.exe` (or `.msi`) | Installs for your user, no administrator rights needed. Uses the Edge WebView2 runtime (part of Windows 11; the installer downloads it if missing). |
| macOS 11+ | `SynapTerm_x.y.z_aarch64.dmg` (Apple Silicon) or `_x64.dmg` (Intel) | Drag SynapTerm to Applications. |
| Linux | `.AppImage`, `.deb` or `.rpm` | The AppImage runs anywhere: make it executable (`chmod +x`) and start it. |

During the beta the builds may not be code-signed yet, so Windows SmartScreen or macOS Gatekeeper can warn the first time. On Windows choose *More info → Run anyway*; on macOS right-click the app and choose *Open*.

## First steps

1. SynapTerm opens with a **local terminal** running your default shell (change it in *Settings → SSH & Shell*).
2. Click **New Server** at the top of the sidebar, pick the protocol and fill in the host. Passwords are optional: with *Authentication* on *None / Ask later*, SynapTerm asks when connecting and offers to save the password in your **system keyring** (never in SynapTerm's own files).
3. **Double-click** the server to connect. The first SSH connection shows the server's key fingerprint: check it and choose *Trust and connect*. It is remembered in `~/.ssh/known_hosts`, as OpenSSH does.
4. Organize servers in **folders** (*New Folder*), mark **favorites** and add **tags** (the tag bar in the sidebar filters the list). **Ctrl+Shift+K** finds any server by name or host.

**Keys and the SSH agent.** *Settings → SSH keys* generates or imports keys (OpenSSH, PEM and PuTTY `.ppk`). Under *SSH agent* you see the keys loaded in the OpenSSH agent or Pageant of this PC, add one (optionally only for 1 hour, 8 hours or a day) and remove them; servers whose authentication is *SSH agent / Pageant* sign with those keys, which never leave the agent. If no agent is running, it tells you how to start the "OpenSSH Authentication Agent" service. *Convert a PuTTY key (.ppk)…* saves a PuTTY key in OpenSSH format, with its `.pub` and a new passphrase if you want one; the original is not changed.

## Tabs and panes

- **Ctrl+T** opens another local terminal; double-clicking a server opens it in a new tab.
- Split a tab with **Ctrl+Shift+E** (right) or **Ctrl+Shift+O** (down) and move between panes with **Alt+arrows**.
- When a connection drops, press **Enter** in the terminal to reconnect.

## Saved commands and the button bar

*Saved commands* (**Ctrl+Shift+J**) keeps the commands you reuse. They can contain variables: `{{host}}`, `{{user}}`, `{{port}}` and `{{alias}}` are filled in from the server, and `{{?name}}` asks for a value each time. Commands marked *in the button bar* appear as buttons under the terminal, grouped in *button sets*: the menu at the left of the bar (for example **Default ▾**) switches to another set, creates one (*New button set…*), renames it or deletes it (its buttons go back to Default). A server can open with its own set: choose the set while connected to it and pick *Open "…" for this server*, like the button bars of SecureCRT. In the editor of a command, *Button set* says where its button goes. The *Command Manager* (**Alt+J**) lists them all. To run one on many servers at once, see [Automation](automation.md). **Ctrl+Shift+M** types into every pane of the tab at once (handy for several servers in a split layout); the command window below does the same with *All sessions* or *Visible panes*.

**Command window.** The ▣ button of the button bar (or *Settings → Terminal → Show the command window*) opens a line under the button bar, like the Command Window of SecureCRT: type the whole command there (the session receives nothing until you press **Enter**), **Shift+Enter** adds another line and **↑ ↓** go through the last commands. *Send to* chooses the active session, the visible panes of the tab or all sessions. It can get a shortcut in *Settings → Keyboard shortcuts*.

**Before connecting and at startup.** In a server's settings, *Before connecting* runs a command on this PC first (for example to connect a VPN or wake the machine up), like the pre-connect command of SecureCRT. By default SynapTerm waits for it to finish (up to the seconds you choose) and only connects if it ends well; if it fails or takes too long, the tab shows its output and why it did not connect (press Enter in the tab to try again). Untick *Wait* for commands that keep running, such as a VPN client. *Open when SynapTerm starts* opens that server by itself every time the app starts.

**On connect.** A server's *On connect* steps run in the terminal as soon as it connects, like the logon script of SecureCRT: *wait for* a text (for example `$ ` or `login:`), then *send* another (`sudo -i`, `cd /srv/{host}`), or *send a password* (from the keyring, or asked each time; never saved in the steps). If a text does not appear in time, the steps stop and the tab says which one. *Then run the script* starts a script of your library on that session. MobaXterm's *Execute command* becomes an on-connect step when you import its sessions.

**Reconnecting.** With *Settings → Terminal → Reconnect automatically* (or per server: *Reconnect automatically* in its settings), an SSH, Telnet or serial tab whose connection drops retries by itself after 3, 5, 10, 20, 30 and then every 60 seconds, and says when; **Esc** cancels it and **Enter** retries at once. Right-click a tab for *Clone session* (another tab to the same server, with a new connection), *Lock session…* (the tab is covered and takes no keys until you type the lock password; the connection stays open and the password is not saved), *Reconnect all* and *Disconnect all* (also assignable to shortcuts); sessions you disconnect yourself are not reconnected automatically.

**Anti-idle.** For servers or firewalls that close idle sessions, a server's *Anti-idle* can use the SSH keepalive with its own interval (nothing is typed), or *send a text when idle*: after that many seconds without anything typed or received, SynapTerm types the text (`\r` = Enter, for example a space or `\r`). Sessions imported from SecureCRT keep their anti-idle.

**Highlighting words.** *Settings → Terminal → Highlight words in the terminal* paints the words you choose (for example `ERROR`, `FAILED`, `WARN`, `ORA-12154`) in a color as they appear; *Add suggested* adds those. Each word can be a regular expression (*Regex*) and match case (*Aa*); each server can add its own words in its settings. Only the screen changes: session logs, Receive ASCII and scripts get the text as it arrives.

**ssh inside a session.** If you type `ssh other-server` in an SSH tab, a yellow line over the resource bar reminds you that the bar still measures the server of the tab, and offers *Save other-server with this one as jump host* (then it opens directly, with its own resource bar and files), or *Open … in its own tab* if it is already saved. It goes away with `exit` or the ×.

**WSL and Docker.** The protocol *WSL (Linux on this PC)* opens a distribution of the Windows Subsystem for Linux (*List distributions* shows the installed ones; leave it empty for the default one), optionally as another user. *Docker container* opens a shell (sh, bash, ash or zsh) inside a container: of this PC (*List containers* shows the running ones) or, with *Runs on* set to one of your SSH servers, of that server, through its SSH connection. `exit` closes the tab.

**S3 buckets.** The protocol *S3 bucket (AWS, R2, MinIO)* opens a bucket in a tab: browse its folders, *Upload…* files (up to 1 GB each; it asks before replacing), *Download* and *Delete*. Fill in the endpoint (for example `s3.eu-west-1.amazonaws.com`, `<account>.r2.cloudflarestorage.com` or `http://192.168.1.10:9000`), the region (`auto` for R2), the bucket and the access key ID; the secret access key goes to the system keyring. Tick *Path-style URLs* for MinIO and R2 (with an IP or a port it is used anyway).

**Old systems: RSH and XDMCP.** Neither is encrypted, so use them only on a trusted network. *RSH / rlogin* logs in to old Unix systems with rlogin (it asks for the password in the terminal) or, with a *Command*, runs it with rsh and closes. *XDMCP* opens the graphical login of a remote Unix in the X server of this PC (VcXsrv or Xming, or the program set in *Settings → SSH & Shell → X server program*); it does not open a tab.

**Mosh.** For a laptop that changes networks or sleeps, choose the protocol *Mosh (SSH + UDP)*: SynapTerm logs in by SSH, starts `mosh-server` and then talks to it over UDP, so the session survives a new wifi, a VPN reconnect or the lid closed for a while; when there is no contact a yellow line says so, and the session goes on by itself when the network is back. The server needs the `mosh` package and UDP ports 60000-61000 open; Mosh cannot go through jump hosts or access routes. *mosh-server command* in the server settings changes how it is started (another path, `-p 60001`…).

**Graphical apps (X11 forwarding).** To open the windows of a Linux server's graphical apps (`xclock`, `gedit`, a Java installer…) on this PC, install and start an X server here (for example VcXsrv or X410), then tick *X11 forwarding* in the server's settings and connect. SynapTerm hands the server a throwaway cookie and checks it on every window it opens, so the server never sees a credential of this PC. If no X server is listening, the terminal says so when it connects. The X server is `localhost:0` by default; change it in *Settings → SSH & Shell → X server display*. The server needs `X11Forwarding yes` in its sshd configuration and `xauth` installed.

**Emulation and mapped keys.** In a server's settings, *Emulation* chooses the terminal type announced to the server (Xterm, VT100, VT220, Linux console or ANSI; by default xterm-256color), for old devices or programs that only know one; the status bar shows it. *Settings → Terminal → Mapped keys* makes a key or combination send a text instead of its usual code: click the key box, press the key (for example **F5**) and type what to send, with `\r` for Enter, `\t` Tab, `\e` Escape and `\xNN` a byte (`\x03` is Ctrl+C). Each server can map its own keys under *Mapped keys*; they win over the global ones.

**Backspace and character set.** In a server's settings, *Backspace sends* chooses what the Backspace key sends: *DEL* (^?, the usual) or *^H*, for old devices that print `^?` or `^H` instead of erasing. *Character set* reads and sends the text of servers that do not use UTF-8 (Latin-1, Latin-9, Windows-1252, CP850 or CP437), so accents and box-drawing characters look right; it works in SSH, Telnet and serial tabs, and the status bar shows it.

**Window and status bar.** *Settings → Window & status bar* makes the whole window see-through (opacity from 30% to 100%, shown as you move it), hides the status bar, or chooses what it shows: connection state, protocol or shell, user@host, character set, columns × rows, terminal type and a clock.

**Ctrl+click** a URL in the terminal to open it in your browser. **Alt+Enter** switches to full screen and back, **Ctrl+=** / **Ctrl+-** / **Ctrl+0** (or Ctrl+mouse wheel) change the text size, and the pin button of the title bar keeps the window always on top. **Terminal menu.** Right-click the terminal for *Copy*, *Paste*, *Open* the URL under the mouse (or in the selection), *Highlight* the selected word, *Find…*, *Clear screen*, *Clear scrollback*, *Reset terminal*, and *Print screen…* or *Print selection…* (just the text, with the session name and the date, through the system print dialog, where you can also save it as PDF), and in SSH, Telnet and serial tabs *Hex view*: a panel below the terminal with every byte received (blue) and sent (amber), in hexadecimal and ASCII, to see what a device really sends or what a key sends; it only records while it is open. With *Paste on right click* on, right-click pastes and **Shift+right-click** opens the menu.

## Sending and receiving text

The **Transfer** menu of the action bar works on the active terminal (SSH, Telnet, serial or local), like the Transfer menu of SecureCRT:

- **Send ASCII…** types a text file into the session line by line, as if you typed it. If the server has a line or character delay (for example imported from SecureCRT), it waits that long between lines or characters. The button bar shows the progress (*file 12/300*); click it to cancel.
- **Receive ASCII…** saves everything that arrives to a file, without colours or escape sequences, until you stop it (the *Receiving …* button in the button bar, or the same menu). It stops by itself if the session closes.

Both actions can get a keyboard shortcut in *Settings → Keyboard shortcuts*.

**Files through the session (Zmodem, Xmodem, Ymodem, Kermit).** In an SSH, Telnet or serial session:

- **Zmodem starts by itself**: run `sz file` on the server and SynapTerm downloads it to the download folder of *Settings → Files* (or asks where, if you chose that); run `rz` and it asks which files to upload. The button bar shows the progress, with a button to cancel.
- For devices that use the older protocols, start the transfer on the other side first and then choose *Transfer → Send with…* (Xmodem, Xmodem-1K, Ymodem, Kermit, Zmodem) or *Receive with…* (Xmodem, Ymodem, Kermit). For example `rx file` then *Send with Xmodem…*, or `sb file` then *Receive with Ymodem…*. Xmodem does not carry the file name, so SynapTerm asks for it when receiving.

**Slow devices.** Some switches and serial consoles lose characters when text arrives too fast. *Settings → Terminal → Pause after each line / character* makes pasting, saved commands and Send ASCII wait between lines or characters (0 = at once), and each server can override it in its settings (empty = the global value; sessions imported from SecureCRT keep theirs). Long sends show their progress in the button bar and can be cancelled there.

**Session log.** *Transfer → Start session log…* saves everything the tab shows to a file until you stop it (the red *Logging …* button in the button bar, or the same menu). To log every connection to a server, open its settings and tick *Session log → Log every connection to a file*: the file name can use `{server}`, `{host}`, `{user}`, `{date}` and `{time}` (or the `%S %H %Y %M %D %h %m %s` of SecureCRT, so imported sessions keep theirs; default `{server}_{date}_{time}.log`). A relative name goes to *Settings → Terminal → Session log folder* (by default `Documents/SynapTerm/logs`). Options: append to the file, the time at the start of each line, and raw (with the escape sequences, for replaying it later). A log that starts on connect includes the login banner.

## Tools

The **Tools** menu of the action bar has the built-in servers and network tools. A green dot on it means a server is listening.

- **Notes**: a notepad with the built-in editor next to the terminal, saved by itself. **Ctrl+Enter** sends the line under the cursor (and moves to the next one) or the selection, line by line, to the terminal of the tab, or to all sessions when *Type in all* is on. Handy to prepare a configuration and paste it step by step.
- **Compare texts or files**: opens a tab with two sides and the differences marked. Type or paste a text on each side, or use *Open file…* on either side to load a file from this PC; the bar at the bottom counts the differences, the arrows jump between them, ⇄ swaps the sides, and you can ignore spaces at line ends or switch to an inline view. In the *Files (SFTP)* view, right-click a remote file for *Compare with a local file…*, or select two files for *Compare the two files* (for example the configuration of a server before and after a change).
- **HTTP, FTP and SFTP (SSH) server**: share a folder of this PC for a while. *HTTP* is read only, for a browser, `curl` or `wget`; *FTP* is for devices that only speak FTP (switches, cameras, old systems); *SFTP* is for `sftp`, WinSCP or FileZilla (no shell or commands). Choose the folder and the port (8080, 2121 and 2222 by default), whether it listens only on this PC or on the whole network, and for FTP and SFTP whether others can upload (they never overwrite a file, and nothing can be deleted or renamed). The dialog shows the address to use, the user `share` and a new password each time (HTTP on this PC only needs none), and for SFTP the fingerprints of its keys. It only listens while it is running: *Stop* closes it, and the activity list shows who logged in and what they downloaded or uploaded.
- **Speed test**: measures the network between this PC and the server of the active SSH tab, downloading, uploading or both for 3 to 20 seconds, with the speed shown live. It goes through the SSH connection, so nothing has to be installed on the server (a Linux or Unix one); encryption is included, which on networks up to 1 Gbps rarely matters.
- **Network scanner**: *Find devices in a network* lists the devices of a range (this PC's network is suggested, for example `192.168.1.0/24`, or `192.168.1.10-50`) that answer on SSH, Telnet, HTTP(S), RDP, SMB or VNC, with their open ports; a /24 takes about 20 seconds. *Open ports of a device* tries `common` ports, `all` of them, or a list such as `22,80,8000-8100`. *Save as server…* opens the new server form filled in (SSH, RDP, VNC or Telnet depending on its ports). Only scan networks and devices you are allowed to.
- **TFTP server**: network devices download from a folder of this PC (firmware, configurations, boot images) and, if you allow uploads, write to it (for example `copy running-config tftp:` on a router). Choose the folder, the port (69 by default) and whether it listens on the whole network or only on this PC, then *Start*. The list shows which device took or sent which file. It only listens while it is running: *Stop* closes the port. Files are never overwritten, and paths outside the folder are refused. Windows may ask to allow it in the firewall the first time.

## Your data

- Servers, folders, tags and settings live in a local database, encrypted with a key kept in the system keyring. Save the **recovery key** from *Settings → Security & Privacy*: without it the database can't be opened on another computer or after the keyring is reset.
- *Settings → Import & export* backs up the server list (JSON or YAML, without passwords) and imports `~/.ssh/config`, CSV/JSON/YAML tables, **MobaXterm** (*Import from MobaXterm* reads the saved sessions of `MobaXterm.ini` with their folders, SSH gateway and key; without passwords) and **SecureCRT**: *Import from SecureCRT* reads its sessions on this PC (folders, ports, users, key files, the button bar of each session, anti-idle) and its button bars, which become button sets. SecureCRT keeps passwords, logon actions and the local command before connecting encrypted, so those are not imported: SynapTerm asks for the password the first time you connect. SecureCRT itself is not changed.
- SynapTerm sends no telemetry. The only request it makes on its own is the optional update check (*Settings → About & updates*).

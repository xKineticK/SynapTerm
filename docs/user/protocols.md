# Protocols

| Protocol | Default port | Opens as |
| --- | --- | --- |
| SSH | 22 | Terminal |
| SFTP | 22 | Terminal with the file browser open |
| Telnet | 23 | Terminal |
| Serial | (COM port or `/dev/tty…`) | Terminal |
| RDP | 3389 | Remote desktop |
| VNC | 5900 | Remote desktop |

## SSH

- **Authentication**: password, private key (with passphrase) or your SSH agent (the OpenSSH agent or Pageant on Windows, `SSH_AUTH_SOCK` on macOS and Linux). *Settings → SSH keys* creates and imports keys, and *Copy public key* in the server form puts the public key on the clipboard, ready for `~/.ssh/authorized_keys`.
- **Host keys** are checked against `~/.ssh/known_hosts`. If a key changes, SynapTerm warns you before connecting: only accept it if you know why (for example, a reinstalled server).
- **Keepalive and timeout** are in *Settings → SSH & Shell*.
- **Jump hosts (ProxyJump)**: in a server's settings, *Jump host* picks another saved server to connect through (that one can have its own jump host, up to 5 in a row). The connection to the target is encrypted end to end; each server uses its own credentials and host key, and SynapTerm asks for each one separately. The terminal, the file browser, tunnels, the editor, the resource bar and *Open in VS Code* (which writes `ProxyJump` in `~/.ssh/config`) all work through it, and importing `~/.ssh/config` links `ProxyJump` to the saved server. The jump host must allow TCP forwarding (`AllowTcpForwarding`); if it doesn't, SynapTerm says so.
- **Access routes** (when the target is only reachable from a shell on another server, e.g. `ssh alias` and then `sudo su -` or `rootsh`): in a server's settings, *How to reach this server → Access route* picks the bridge server and lists the steps: *Wait for* a text (a regular expression, with a timeout), *Send* a text (`{host}`, `{user}`, `{port}` and `{alias}` are replaced by this server's) or *Send secret* (this server's saved password, the bridge's, or asked every time for one-time codes). Secrets come from the system keyring and are never stored in the route. SynapTerm connects to the bridge and runs the steps in the terminal; if one fails, it says which step and what it received. *● Record route* in the tab bar records what you type while you log in by hand and creates a server with those steps (typed passwords are not recorded).

## Files (SFTP)

**Files (SFTP)** in the tab bar (**Ctrl+Shift+B**) opens a file browser over the same connection: browse, upload files or folders (also by dragging them onto it), download (right-click; double-click opens a file in the editor), rename, delete and create folders. *Synchronize* (⇄) copies a local folder to the server or the other way round, and shows what will change before doing it.

## Server resources

SSH tabs show a one-line bar at the bottom with the server's state: host, uptime, load, CPU (with a small graph), memory used/total, the fullest disk and network traffic.

- Click an item for its details card; several cards can be open at once and share the space above the bar (Escape closes them): **Disks** (every file system with size, used, free, % and inodes), **CPU** (per core, load average and the processes that use the most CPU or memory), **Memory** (like `free -m`) and **Network** (traffic per interface).
- Items turn yellow or red past the warning and critical levels (80 % and 90 % by default; CPU uses the average of the last samples, so a short spike doesn't count). The first time a disk passes the critical level you get a notification.
- Nothing is installed on the server: a separate SSH channel runs read-only commands (`/proc`, `df`, `ps`; `sysctl` and `vm_stat` on macOS and FreeBSD; PowerShell on Windows) every 3 seconds. They never show up in the terminal or its history. The process list is only read while the CPU details are open.
- *Settings → Resource monitor* turns it on or off for every server, and sets the refresh interval and the levels. Each server can override it in its settings (*Resource monitor*). On network devices and other systems it doesn't recognise, the bar simply doesn't appear.

## Editing files and VS Code

- **Integrated editor**: double-click a file in the file browser (or *Open in editor*) to open it in an editor pane next to the terminal. It's the editor of VS Code (Monaco): syntax highlighting, find and replace (**Ctrl+F** / **Ctrl+H**), multiple cursors (**Alt+click**, **Ctrl+D**) and the usual VS Code shortcuts. It loads the first time you open a file.
  - **Ctrl+S** saves over SFTP keeping the encoding (UTF-8, UTF-8 with BOM, UTF-16, Windows-1252), the line endings (LF or CRLF, shown in the status bar; click to change them) and the file's permissions and owner.
  - If the file changed on the server since you opened it, SynapTerm asks before overwriting it (or loads the server version).
  - A ● in the file tab marks unsaved changes; closing it, its pane or the SSH tab asks first. **Ctrl+W** closes the current file; the × at the right of the file tabs closes the editor pane, and it also closes by itself with its last file. The terminal and its connection stay open (the × of the tab at the top, which shows the connection, closes everything).
  - Binary files and files over 5 MB are not opened: SynapTerm offers to download them.
  - The editor pane splits (**Ctrl+Shift+E** / **Ctrl+Shift+O**) and resizes like any other pane; both halves can show the same file.

- **IDE mode** (**IDE** in the tab bar, **Ctrl+Shift+D**) turns an SSH tab into a small IDE: the file explorer on the left, the editors in the middle and the terminal at the bottom (drag the borders to resize them). Each tab remembers it; press it again to get the usual terminal view back.
  - *Follow terminal* at the top of the explorer: **Follow cd** moves the explorer whenever the terminal changes folder; **Fixed folder** keeps it where it is, and *Go to terminal folder* jumps there once. The choice is remembered per server.
  - **Ctrl+P** (*Go to file*) lists the files under the explorer folder (with `find` on the server) and filters them as you type, like in VS Code; **Enter** opens the file in the editor.
  - **Ctrl+Shift+F** (or the *Search* tab of the sidebar) searches text in those files (with `grep` on the server): match case, whole word and regular expressions; results grouped by file, click one to open the file at that line. Searches run in the background on their own SSH channel (the terminal stays usable) and can be cancelled. Folders such as `.git` and `node_modules` are skipped, and only the first 2,000 matches (20,000 files for Ctrl+P) are shown.
  - These two shortcuts work when the focus is outside the terminal; inside it, **Ctrl+P** and **Ctrl+Shift+F** keep doing what they did (previous command in the shell, find in the terminal).
  - The terminal folder comes from the shell (OSC 7). Many servers don't send it: *Folder not reported — enable…* adds a few lines to `~/.bashrc` or `~/.zshrc` (only if you accept) and works from the next shell (reconnect or run `source ~/.bashrc`).
- **Open in VS Code** (tab bar, or *Open in VS Code (Remote - SSH)* on a folder or file of the file browser) opens the server in [VS Code](https://code.visualstudio.com/) with Microsoft's *Remote - SSH* extension, in the folder of the terminal (or of the file browser). SynapTerm offers to install the extension if it's missing.
  - It adds the server to `~/.ssh/config`, inside a block marked *SynapTerm* at the top of the file (host `synapterm-<name>`; servers added before the rename keep their `koreshell-<name>` host). Your own entries are never changed; don't edit inside the block, SynapTerm rewrites it.
  - With a key, VS Code uses the same key. With the SSH agent, VS Code uses the system's agent (on Windows, the *OpenSSH Authentication Agent* service, not Pageant). With a password, VS Code asks for it: it can't read the passwords SynapTerm keeps in the system keyring.
  - The terminal folder is known when the shell reports it (OSC 7); otherwise SynapTerm uses the folder of the file browser or your home folder.
- **Edit with VS Code / Edit with the system editor** (right-click a file in the file browser) downloads a private copy and opens it in that editor. Every time you save, SynapTerm uploads it and shows a notification; permissions and owner of the file are kept. If the file changed on the server since you opened it, SynapTerm asks before overwriting it. Closing the SSH tab stops watching the file and deletes the copy. The system editor is Notepad on Windows, the default text editor on macOS and `xdg-open` on Linux. Files over 10 MB can't be edited (download them instead).

## Tunnels (port forwarding)

**Tunnels** (**Ctrl+Shift+L**) in an SSH tab:

| Type | Equivalent | Use |
| --- | --- | --- |
| Local | `ssh -L 8080:db:5432` | Reach a service the server can see (a database) on `localhost` |
| Remote | `ssh -R 9000:localhost:3000` | Let the server reach a service on your computer |
| SOCKS5 | `ssh -D 1080` | Browse through the server |

Tunnels can be saved on the server and started automatically when connecting.

## Telnet

For network devices without SSH. Telnet sends everything, passwords included, **in clear text**. *Log in automatically* answers the login and password prompts with the saved credentials.

## Serial console

Pick the port (SynapTerm lists the ones it finds), the baud rate (9600 to 115200 and more), data bits, parity, stop bits and flow control. **Send BREAK** in the tab bar is there for devices that need it (password recovery on network gear, for example).

## RDP (Windows Remote Desktop)

- Connects with the saved password or asks for it. Use `DOMAIN\user` or the *Domain* field for domain accounts.
- The first connection shows the certificate fingerprint (RDP servers usually have self-signed certificates). SynapTerm remembers it and warns if it changes.
- The resolution follows the size of the tab, or a fixed one chosen in the server form.
- Your **keyboard layout** is used in the remote session. Alt+Tab, the Windows key, Alt+F4 and Ctrl+Esc go to the remote computer while it has the focus. **Ctrl+Alt+End** sends Ctrl+Alt+Del.
- **Clipboard**: copy and paste text in both directions (can be turned off per server).
- **Files**: with OpenSSH Server enabled on Windows, *Files (SFTP)* browses the remote computer's files over SSH.
- Saved commands are typed into the remote window that has the focus.

## VNC

- Works with TightVNC, UltraVNC, TigerVNC, x11vnc, macOS Screen Sharing (with "VNC viewers may control screen with password" enabled) and most other VNC servers.
- Security: none or VNC password. Only the first **8 characters** of a VNC password count. Other methods (TLS/VeNCrypt, Apple ID login) aren't supported yet: enable the VNC password on the server.
- VNC doesn't encrypt the screen or the keyboard. Use it on a trusted network, over a VPN or through an SSH tunnel.
- Options per server: *View only*, *Share the desktop with other viewers* and *Share the clipboard* (text; characters outside Latin-1 arrive as `?`).
- The image is scaled down to fit the tab. Ctrl+Alt+End sends Ctrl+Alt+Del.

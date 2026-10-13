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

## Tabs and panes

- **Ctrl+T** opens another local terminal; double-clicking a server opens it in a new tab.
- Split a tab with **Ctrl+Shift+E** (right) or **Ctrl+Shift+O** (down) and move between panes with **Alt+arrows**.
- When a connection drops, press **Enter** in the terminal to reconnect.

## Saved commands and the button bar

*Saved commands* (**Ctrl+Shift+J**) keeps the commands you reuse. They can contain variables: `{{host}}`, `{{user}}`, `{{port}}` and `{{alias}}` are filled in from the server, and `{{?name}}` asks for a value each time. Commands marked *in the button bar* appear as buttons under the tabs. **Ctrl+Shift+M** types into every pane of the tab at once (handy for several servers in a split layout).

## Your data

- Servers, folders, tags and settings live in a local database, encrypted with a key kept in the system keyring. Save the **recovery key** from *Settings → Security & Privacy*: without it the database can't be opened on another computer or after the keyring is reset.
- *Settings → Import & export* backs up the server list (JSON or YAML, without passwords) and imports `~/.ssh/config` or CSV/JSON/YAML tables.
- SynapTerm sends no telemetry. The only request it makes on its own is the optional update check (*Settings → About & updates*).

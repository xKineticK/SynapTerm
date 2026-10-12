# SynapTerm

**Secure, AI-assisted connections.** SynapTerm is a desktop terminal and session manager: local terminals, SSH with file transfer and tunnels, Telnet, serial consoles and remote desktops (RDP and VNC), all in tabs and split panes, with an AI assistant that only does what you allow.

## Download

Get the installer for your system from the **[latest release](https://github.com/xKineticK/SynapTerm/releases/latest)**:

| System | File |
| --- | --- |
| Windows 10/11 | `SynapTerm_x.y.z_x64-setup.exe` (or the `.msi`) |
| macOS 11+ | `SynapTerm_x.y.z_aarch64.dmg` (Apple Silicon) or `SynapTerm_x.y.z_x64.dmg` (Intel) |
| Linux | `.AppImage`, `.deb` or `.rpm` |

SynapTerm can check for updates by itself (*Settings → About & updates*); updates are signed and verified before they are installed.

## What it does

- **SSH** with keys, the SSH agent, jump hosts and access routes (`sudo su -` and similar), **SFTP** file browser, editor and **tunnels**
- **Telnet**, **serial** consoles, **RDP** and **VNC** remote desktops
- Saved servers with folders and tags, quick search (`Ctrl+Shift+K`), broadcast typing to several sessions, command history and buttons
- Resource bar for SSH servers (CPU, memory, disks, network) without installing anything on them
- **AI assistant**: use your own Claude Code (as a chat or in a terminal), Gemini CLI or Codex, or your own API key. The assistant asks before reading a tab or running a command, never sees your passwords, and every action is logged
- Passwords and keys stay in your system keychain; the server list is encrypted. **No telemetry**

## Documentation

- [User guide](docs/user/README.md): [getting started](docs/user/getting-started.md), [protocols](docs/user/protocols.md), [keyboard shortcuts](docs/user/shortcuts.md), [FAQ](docs/user/faq.md)

## Support

Found a bug or have an idea? Open an [issue](https://github.com/xKineticK/SynapTerm/issues/new/choose). In the app, *Settings → About & updates → Report a problem* prepares a report (version, system, recent warnings) you can review and paste.

---

This repository holds the releases and the documentation of SynapTerm; the source code is not public.
© 2026 SynapTerm. All rights reserved.

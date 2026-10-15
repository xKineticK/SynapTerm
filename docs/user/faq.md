# FAQ and troubleshooting

## Connections

**"Connection refused" or "timed out".** The tab shows what failed (name resolution, network or port) and what to check. Make sure the host and port are right, that the service is running and that no firewall blocks it. *Test connection* in the server form runs the same checks without sending any credentials.

**"Host key has CHANGED".** The server presents a different key than the one saved in `~/.ssh/known_hosts`. If the server was reinstalled, accept the new key; if not, don't connect and ask its administrator. Trusted keys are listed in *Settings → Known hosts*.

**The password is asked every time.** Tick *Save password in the system keyring* in the password prompt, or set it in the server form (*Authentication → Password*).

**The connection dropped.** Press **Enter** in the terminal to reconnect. Idle connections are kept alive with SSH keepalives (*Settings → SSH & Shell*), but some firewalls still close them.

## Remote desktops

**RDP: the remote computer types the wrong characters.** Windows fixes the keyboard layout when the remote session is created. Sign out of the remote session once (Start → Sign out) and connect again.

**VNC: "the server only offers … authentication".** The server requires a method SynapTerm doesn't support yet (TLS, Apple ID, RealVNC accounts). Enable classic VNC password authentication on the server.

**VNC: the password is rejected but it's right.** VNC passwords only use the first 8 characters, and some servers block new attempts for a while after several failures.

## Data and privacy

**Where is my data?** Windows: `%APPDATA%\com.msark.koreshell`; macOS: `~/Library/Application Support/com.msark.koreshell`; Linux: `~/.local/share/com.msark.koreshell`. Logs are in its `logs` folder (*Settings → Security & Privacy* shows the path).

**"Server list recovered" at startup.** The database file was damaged (for example after a crash or a full disk). SynapTerm kept the damaged file next to the new one (`session_manager.corrupt-….sqlite`) and started with an empty list: restore your last export from *Settings → Import & export*.

**"Database locked" at startup.** The database is encrypted and its key isn't in this computer's keyring (a new computer, or the keyring was reset). Unlock it with your **recovery key** (`KSK1-…`).

**Does SynapTerm send anything?** No telemetry and no crash reports. Only if you enable *Check for updates when SynapTerm starts* does it ask GitHub for the latest version; nothing about you or your servers is sent.

## Updates

*Settings → About & updates → Check for updates now*. Updates come from GitHub Releases and are verified with SynapTerm's signing key before they are installed. SynapTerm restarts to finish, which closes the open sessions.

## Reporting a problem or suggesting an idea

Click **Feedback** at the right of the action bar (also *Settings → About & updates → Report a problem*). Choose *Report a problem* or *Suggest an idea*, describe it and press *Send* (or Ctrl+Enter). It goes to the SynapTerm team with the app version and your system. Your email is optional and only used to reply.

The app log is attached only if you tick *Attach the app log*: it's the last 200 lines, without passwords, and *See exactly what is sent* shows it before you send. Reports we work on are published as [GitHub issues](https://github.com/xKineticK/SynapTerm/issues), never with your email. You can also open an issue there yourself; never paste passwords, private keys or recovery keys in one.

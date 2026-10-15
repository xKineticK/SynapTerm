# Automation: scripts and batches

SynapTerm can run **Python scripts** that type in your sessions, wait for their output, connect to your saved servers and copy files — on one server or on many at once. Scripts never see your passwords, passphrases or keys: they use the connections SynapTerm opens for them.

- [Before you start](#before-you-start)
- [The script library](#the-script-library)
- [Running a script](#running-a-script)
- [Running on several servers](#running-on-several-servers)
- [The `synapterm` module (API v1)](#the-synapterm-module-api-v1)
- [Examples](#examples)
- [Security](#security)

## Before you start

Scripts use the **Python installed on your computer** (3.9 or newer). Open **Settings → Automation** and press **Check**: SynapTerm looks for `py -3`, `python` and `python3`. If your Python is somewhere else, write its full path in the field. Install it from [python.org](https://www.python.org/downloads/) if you do not have it (on Windows, tick *Add python.exe to PATH*).

In the same section:

- **Maximum run time**: a script still running after this is stopped, together with the sessions it opened (30 minutes by default).
- **Servers at once in a batch**: how many servers a batch runs on at the same time (5 by default).
- **Show log**: opens the automation log (see [Security](#security)).

## The script library

Only scripts in your library can run. Open it from the **Scripts** icon in the sidebar (or press **Alt+I** to show the sidebar).

- **New Script** creates a `.py` file from a template that explains the basics.
- **Add .py** copies a script you already have into the library.
- **⋯ → Show in folder** opens the library folder, so you can edit the script with your editor. Changes are picked up the next time it runs.
- The first line of the script's docstring (or its first comment) is shown under its name.

## Running a script

- **▶** next to a script runs it in the **active tab** (`app.session` in the script). It can also run with no tab open: then `app.session` is `None` and the script opens its own connections.
- **⋯ → Run with arguments…** passes arguments (`app.args`). Use quotes for an argument with spaces.
- **⋯ → Button and shortcut…** adds a ▶ button for the script to the button bar under the terminal and gives it its own keyboard shortcut.
- In the command palette (**Ctrl+Shift+K**), type `>` and part of the script name.

The **script console** opens under the terminal: one tab per run, with its live output and its state (*Running*, *Done*, *Error*, *Stopped*, *Timed out*). **Stop** ends a script and everything it started.

Do not use `input()` to ask something: it shows a SynapTerm dialog instead (same as `app.prompt()`).

## Running on several servers

A **batch** runs a saved command or a script on many SSH servers:

- For a **saved command**: right-click its button in the button bar, or open the *Command Manager* (**Alt+J**), choose the command and press *Run on servers…*.
- For a **script**: **⋯ → Run on servers…**

Pick the servers by folder, tag, text or one by one, choose how many run at once, press **Review** to see the list of targets and then **Run**. Each server gets its own connection without a tab — with its saved password, key or SSH agent — that is closed at the end. A command's variables (`{{host}}`, `{{alias}}`…) are filled in for each server, and `{{?questions}}` are asked once for all of them.

The console shows one row per server: **OK**, **Error** (with the reason: the server is down, the password is not saved, the command failed…) or **Timed out**, how long it took and its output (click the row). **Export CSV** and **Export JSON** save the report.

A command in a batch runs in the server's shell (bash, sh, zsh…). Its exit code decides OK or Error. For network devices (switches, routers) use a script, which can wait for the device's own prompt.

## Recording a macro

The **Macros** panel of the sidebar (**Alt+U**) records what you type in the active tab: press *Record a macro*, work as usual, then *Stop and save* and give it a name. It is saved as a script of your library that waits for the same prompts and types the same lines, so you can edit it like any other script. Passwords and codes you type without echo are never saved: the macro asks for them when it runs. The panel lists the recorded macros; ▶ runs one in the active tab.

## The `synapterm` module (API v1)

SynapTerm makes the `synapterm` module available to your scripts. Patterns are regular expressions (like Python's `re`, without look-around); `^` and `$` match at the start and end of each line, and `(?i)` makes them case-insensitive.

```python
from synapterm import app
```

### `app`

| | |
| --- | --- |
| `app.session` | The session (tab) the script was started from, or `None`. |
| `app.args` | List of the script's arguments. |
| `app.servers(folder=None, tag=None, query=None)` | Saved servers, as `Server` objects (`id`, `alias`, `host`, `port`, `username`, `protocol`, `folder`, `tags`). `folder` matches a folder name or path and includes its subfolders; `query` looks in the alias, host and user. |
| `app.connect(server, tab=False, timeout=60)` | Connects to a saved server (alias or id) and returns a `Session`. Without a tab it uses the saved password, key or agent and never asks anything: if the password is not saved or the host key has not been trusted yet, it raises an error that says so. `tab=True` opens a normal tab, which can ask for what is missing. Sessions without a tab are closed when the script ends. |
| `app.sftp(session)` | File transfers through an SSH session (`Sftp`). |
| `app.message(text, kind="info")` | Shows a notice in SynapTerm (`info`, `success` or `error`). |
| `app.prompt(question, secret=False, default="")` | Asks the user and returns the answer. Raises `KeyboardInterrupt` if they cancel. |

### `Session`

A session keeps a **read position**: `wait_for`, `read_until` and `read` only look at what arrived after the last thing they returned (or after the last `send`). A session from `connect()` starts at its first output (the banner and the first prompt); `app.session` starts at what arrives after the script starts.

| | |
| --- | --- |
| `send(text)` | Types `text` (add `\r` for Enter). |
| `sendline(text="")` | Types `text` and presses Enter. |
| `wait_for(pattern, timeout=30)` | Waits for `pattern` and returns a `Match` (`text`, `groups`, `group(n)`). Raises `TimeoutError`, or `ConnectionError` if the session closes first. |
| `read_until(pattern, timeout=30)` | Everything written until `pattern` (included). |
| `read()` | What arrived since the last read, without waiting. |
| `close()` | Closes a session opened without a tab. |
| `id`, `headless` | Its id, and whether it has no tab. |

### `Sftp`

| | |
| --- | --- |
| `upload(local_path, remote_dir)` | Copies a local file or folder into `remote_dir`. |
| `download(remote_path, local_dir=".")` | Copies a remote file or folder into `local_dir`. |
| `list(remote_dir=None)` | Entries of a remote folder (the home folder by default): `name`, `path`, `kind`, `size`… |

Errors that SynapTerm reports are raised as `synapterm.SynapTermError` (or `TimeoutError` / `ConnectionError`).

## Examples

### Check the disks of every server with a tag

Run it from the library with arguments, e.g. `web 85` (tag and warning level).

```python
"""Disk usage of every server with a tag (arguments: tag, warning %)."""
from synapterm import app

tag = app.args[0] if app.args else "web"
limit = int(app.args[1]) if len(app.args) > 1 else 85
PROMPT = r"[$#] ?$"

for server in app.servers(tag=tag):
    try:
        s = app.connect(server.alias)
        s.wait_for(PROMPT, timeout=20)
        s.sendline("df -P / | tail -1")
        m = s.wait_for(r"(\d+)% /\s*$", timeout=20)
        used = int(m.group(1))
        print(f"{server.alias:<20} {used:>3}%{'  <- check' if used >= limit else ''}")
        s.close()
    except Exception as error:
        print(f"{server.alias:<20} error: {error}")
```

The same check as a **batch** of a saved command, without a script: save `df -P / | tail -1` as a command and run it on the servers of the tag.

### Back up the configuration of the switches

Saves the running configuration of every server with the `switch` tag to a folder on your computer (argument: the folder). Adjust the commands and the prompt to your devices.

```python
"""Save the running config of the switches (argument: local folder)."""
import datetime
import os
from synapterm import app

folder = app.args[0] if app.args else os.path.expanduser("~/switch-backups")
os.makedirs(folder, exist_ok=True)
stamp = datetime.date.today().isoformat()
PROMPT = r"[>#]\s*$"

for switch in app.servers(tag="switch"):
    try:
        s = app.connect(switch.alias)
        s.wait_for(PROMPT, timeout=30)
        s.sendline("terminal length 0")
        s.wait_for(PROMPT)
        s.sendline("show running-config")
        config = s.read_until(PROMPT, timeout=120)
        path = os.path.join(folder, f"{switch.alias}-{stamp}.cfg")
        with open(path, "w", encoding="utf-8") as f:
            f.write(config)
        print(f"{switch.alias}: saved {path}")
        s.close()
    except Exception as error:
        print(f"{switch.alias}: {error}")

app.message(f"Switch backups in {folder}", kind="success")
```

## Security

- Only scripts in your library run. The free plan keeps up to 8 scripts and 8 macros; SynapTerm Pro (or the beta) has no limit and adds running a command or script on several servers at once.
- A script can only use the session it was started from and the sessions it opens itself.
- The API has no way to read saved passwords, passphrases, private keys or the key that encrypts your server list.
- Running on several servers always shows the list of targets first.
- Every run has a time limit and can be stopped; the sessions it opened are closed when it ends, even if it fails.
- Every script run and batch is written to `automation-audit.log` (**Settings → Automation → Show log**): the script or command, where it ran and how it ended — never its output or the values of its arguments.

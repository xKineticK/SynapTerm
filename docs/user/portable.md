# Portable copy for Windows

The portable copy is SynapTerm in a folder: unzip it anywhere you can write (a folder of yours or a USB drive) and open `SynapTerm.exe`. Nothing is installed, nothing goes to the registry or to the Windows Credential Manager, and all your data stays in the folder, next to the program. It updates itself in that same folder.

Download *Portable (.zip)* from [synapterm.com/download](https://synapterm.com/download) or from your invitation page. It needs Windows 10/11 x64 with the Edge WebView2 runtime (part of Windows 11; on Windows 10 it usually comes with Edge).

## What is in the folder

| Item | What it is |
| --- | --- |
| `SynapTerm.exe` | The app. |
| `portable` | The marker that makes this copy portable. Keep it next to `SynapTerm.exe`. |
| `bin\synapterm.exe` | The `synapterm` command used by the AI agents. |
| `bin\python\` | Python 3.14 with pip, for your scripts (see below). |
| `data\` | Everything of yours: servers, settings, scripts, logs, your vault and the browser data of the app (`data\webview`). SynapTerm creates it. |

To move the copy, move the whole folder. To back it up, copy the whole folder (with SynapTerm closed). To remove it from a PC, delete the folder.

The program is not code-signed yet: the first time you open a copy unzipped with Windows Explorer, SmartScreen may warn. Choose *More info → Run anyway*.

## The master password

The first time, SynapTerm asks you to create a **master password** (at least 8 characters). Your passwords, API keys and license are kept in `data\vault.json`, encrypted with a key derived from it (Argon2id). Then it shows a **recovery key** (`KSK1-…`): save it in a password manager or another safe place, **not** in this folder. Without the password or the recovery key, nobody can open your data, not even you.

- SynapTerm asks for the password every time it starts (the first screen is in the language of Windows: your language setting is inside the vault).
- **Forgot it?** On the unlock screen, *I forgot the password…* lets you paste the recovery key and choose a new password. Your data stays.
- **Change it** in *Settings → Security & Privacy → Change master password…* (it asks for the current one). *Show recovery key…* also asks for the password.
- **Start over…** (on the unlock screen) moves your encrypted data aside in `data\` (as `vault.lost-…` and `session_manager.lost-…`, not deleted) and starts empty. Only the recovery key can open those files again. You have to activate your license again (it does not use another device).
- If the folder is read-only, SynapTerm cannot keep its data and does not start: copy the folder to a drive where you can write.

## Remember on this computer

On the unlock screen (and when creating the vault or recovering it), *Remember on this computer* makes SynapTerm open without the password **for your Windows user on this computer**. The key is kept in `data\remembered.json`, protected by Windows (DPAPI) for your user: another user, another computer or a copy of the folder elsewhere still asks for the password. The file does not name the computer or the user; each entry carries an internal identifier of your Windows user's DPAPI key, which changes every few months.

- Anyone who can sign in to your Windows account on that computer can open the copy without the password. **Don't use it on a shared or borrowed computer.**
- *Settings → Security & Privacy → Forget on this computer* stops it on this computer.
- Recovering with the recovery key or starting over forgets it on **every** computer. Changing the password does not: use *Forget* on each computer, or recover, if you want the others to ask again.

## License

Activate it in *Settings → License* as usual. In the portable copy the license is kept inside the vault, and the copy counts as **one device**, whatever PC you plug it into. After *Start over* you activate it again.

## Updates

As in the installed version (*Settings → About & updates*): SynapTerm downloads the new zip, checks its signature, closes, replaces `SynapTerm.exe`, `bin\synapterm.exe` and `bin\python\` in this folder and opens again. `data\` is not touched. If another window of the same copy is open, it asks you to close it first. If a script or a `python.exe` of this copy is running, the update stops the scripts; a `python.exe` you opened by hand makes the update fail and leaves the folder as it was: close it and try again. If the folder is read-only, it offers to download the zip so you can replace the files yourself (keep `data\`).

## Python and pip

Your scripts use the **Python that comes in the copy** (`bin\python`, Python 3.14 from python.org with pip 26.3), so they work on any PC, with or without Python installed. *Settings → Automation → Check* shows which one is used. `import synapterm` works without setting anything up.

- **Your own Python**: write its path in *Settings → Automation*. A relative path with `\` (for example `tools\python\python.exe`) is inside the copy's folder, so it also travels on the USB drive. If that path does not answer, the scripts use the embedded one.
- **Installing packages**: in a local terminal of SynapTerm, `python` and `pip` are the copy's ones: run `python -m pip install <package>`. Packages go to `data\python\py314\` and survive updates. pip's cache is in `data\python\pip-cache`. pip only talks to PyPI when you run it.
- From any other console, use the full path: `<folder>\bin\python\python.exe -m pip install <package>` (packages also go to `data\`).
- If the drive letter changes (USB on another PC), `python -m <tool>` keeps working, but the `.exe` launchers that pip creates in `data\python\py314\Scripts` stop working: use `python -m` instead.
- Packages without a wheel for Windows need a C compiler, which most PCs don't have.
- When a SynapTerm update brings a **new minor version of Python** (3.14 → 3.15), the release notes say so and you have to **install your packages again** (they go to `data\python\py315`; you can delete `py314`). A fix version (3.14.N → 3.14.M) keeps them.
- Not included: `venv`, `ensurepip` and `tkinter` (with IDLE).
- A `PIP_PREFIX` or `PIP_CACHE_DIR` variable set on the PC wins over the copy's ones, and then packages or the cache go where it says.
- Python's license (PSF) is in `bin\python\LICENSE.txt`.

## What is written outside the folder

When it starts, SynapTerm writes nothing outside its folder, apart from temporary files in `%TEMP%` and what WebView2 itself does. These things you ask for do write on the PC:

- Downloads (SFTP, Zmodem, S3) go to the folder you choose (*Downloads* by default).
- *Edit with…* works on temporary copies in `%TEMP%`.
- *Open in VS Code* adds the server to `%USERPROFILE%\.ssh\config`.
- Session logs go to the folder you choose.
- When you trust an SSH server's key, it is remembered in `%USERPROFILE%\.ssh\known_hosts` of that PC, as OpenSSH does.
- If the PC has a `WEBVIEW2_USER_DATA_FOLDER` environment variable, WebView2 keeps the app's browser data there instead of in `data\webview`.

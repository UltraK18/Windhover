# Windhover User Scripts

## What are user scripts?

User scripts let you extend Windhover with your own automations. Drop an executable script file (`.bat`, `.ps1`, `.py`, `.sh`, `.js`) into Windhover's scripts folder, bind a keyboard shortcut, and the script will run against the image you're currently viewing or the images you've selected in the grid. Typical use cases include opening an image in Photoshop, copying its prompt to the clipboard, batch-renaming files, uploading to a server, applying a watermark, or piping metadata into another tool.

## Quick start

1. Open **Settings > Scripts** in Windhover.
2. Click **Open Folder** — this reveals Windhover's scripts folder in File Explorer.
3. Create a new file in that folder ending in `.bat`, `.ps1`, `.py`, `.sh`, or `.js`.
4. Write your script (see the samples below for working examples you can copy).
5. Save the file and click **Refresh** in Settings > Scripts so Windhover picks it up.
6. Assign a keyboard shortcut by clicking the shortcut button next to your script and pressing the desired keys.
7. Press the shortcut while viewing an image (or with images selected in the grid) and your script runs.

Tip: click **Add sample scripts** in Settings > Scripts to drop a set of ready-to-use examples into the folder.

## Supported script types

| Extension | Runtime | When to use |
|-----------|---------|-------------|
| `.bat`, `.cmd` | `cmd.exe` (built-in) | Simplest option — recommended for beginners |
| `.ps1` | PowerShell (built-in on Win10/11) | More power than batch, GUI dialogs, clipboard access |
| `.py` | Python (must be installed) | Image processing, complex logic |
| `.sh` | `sh` (Git Bash / WSL) | Unix-style scripts |
| `.js` | Node.js (must be installed) | JavaScript ecosystem |

`.bat` and `.ps1` work out of the box on all modern Windows machines. The others require the matching runtime to be installed separately and available on your `PATH`.

## How scripts are executed

When you trigger a script, Windhover:

- Passes the image's absolute path as the **first argument** to the script (`%1` in batch, `$1` in shell/python, `$args[0]` in PowerShell, `process.argv[2]` in Node).
- Exposes additional metadata via `WINDHOVER_*` environment variables (see the reference below). If a piece of metadata isn't available for the current image, the corresponding variable is empty.
- Sets the script's working directory to the scripts folder.
- Treats exit code `0` as success and any non-zero exit code as an error.

Windhover supports two run modes:

- **Detached mode (default, ON).** The script is spawned and Windhover returns immediately without waiting. Good for scripts that open an external application (Photoshop, an editor, a browser tab). You won't see any stdout/stderr from the script in this mode — it runs fire-and-forget.
- **Capture mode.** Windhover waits for the script to finish and captures its stdout, stderr, and exit code. Use this when you want Windhover to report the result. Turn off the **Run detached** toggle in Settings > Scripts to make the script run in capture mode.

Tip: click the **Test** button next to a script in Settings > Scripts to run it once in capture mode and see the output in a popup — handy while you're iterating on a script without toggling detached mode back and forth.

## Environment variables reference

Every `WINDHOVER_*` variable is set as a process environment variable before the script runs. If the current image has no value for a given field (for example, no parsed prompt), the variable is set to an empty string.

| Name | Description | Example value |
|------|-------------|---------------|
| `WINDHOVER_FILE` | Full image path (also passed as first argument) | `C:\Pictures\render_001.png` |
| `WINDHOVER_DIR` | Directory containing the image | `C:\Pictures` |
| `WINDHOVER_FILENAME` | Filename with extension | `render_001.png` |
| `WINDHOVER_NAME` | Filename without extension | `render_001` |
| `WINDHOVER_EXT` | File extension (including the dot) | `.png` |
| `WINDHOVER_SELECTION` | All selected image paths, newline-separated | `C:\a.png\nC:\b.png` |
| `WINDHOVER_COUNT` | Number of selected images | `3` |
| `WINDHOVER_MODEL` | Generation model name | `animagineXL_v31` |
| `WINDHOVER_SEED` | Seed value | `1234567890` |
| `WINDHOVER_RATING` | Star rating (0–5) | `4` |
| `WINDHOVER_TAGS` | Tags, comma-separated | `1girl, solo, smile` |
| `WINDHOVER_WIDTH` | Image width in pixels | `1024` |
| `WINDHOVER_HEIGHT` | Image height in pixels | `1536` |
| `WINDHOVER_PROMPT` | Positive prompt text | `masterpiece, 1girl, ...` |

## Keyboard shortcuts

- Bind a shortcut via **Settings > Scripts > shortcut button**, then press the key combo you want.
- Modifiers `Ctrl`, `Shift`, and `Alt` are supported and can be combined.
- Matching is **IME-safe**: it uses physical key codes, so `Ctrl+S` works correctly regardless of whether your IME is in Korean, Japanese, or any other input mode.
- Shortcuts apply both in the image viewer and in the grid view.
- Shortcuts will not fire while you're typing in an input field (search bar, tag editor, etc.).

## Samples

Each sample below is a complete script — copy the code into a file with the given name in your scripts folder and click **Refresh**.

### 1. Reveal in File Explorer — `sample-reveal-in-explorer.bat`

```bat
@echo off
explorer /select,"%1"
```

Opens the image's folder with the file highlighted. No dependencies.

### 2. Copy prompt to clipboard — `sample-copy-prompt.ps1`

```powershell
$env:WINDHOVER_PROMPT | Set-Clipboard
```

One line of PowerShell — reads the positive prompt from the environment and copies it to the system clipboard.

### 3. Show image info dialog — `sample-show-info.ps1`

```powershell
Add-Type -AssemblyName System.Windows.Forms
$info = "File: $env:WINDHOVER_FILENAME`nSize: $($env:WINDHOVER_WIDTH)x$($env:WINDHOVER_HEIGHT)`nModel: $env:WINDHOVER_MODEL`nSeed: $env:WINDHOVER_SEED"
[System.Windows.Forms.MessageBox]::Show($info, "Windhover Info")
```

Pops up a Windows Forms message box with the current image's metadata. Pure PowerShell, no installs needed.

### 4. Copy to Desktop — `sample-copy-to-desktop.bat`

```bat
@echo off
copy /Y "%1" "%USERPROFILE%\Desktop\"
```

One-click backup of the current image to your desktop.

## Python example (with Pillow)

A slightly more advanced example — convert the current PNG to a JPEG next to the original:

```python
import sys
from PIL import Image

src = sys.argv[1]
Image.open(src).convert("RGB").save(src.rsplit(".", 1)[0] + ".jpg", quality=95)
```

Save as `convert-to-jpg.py`. Requires Pillow: `pip install Pillow`.

## Debugging scripts

If a script doesn't seem to do anything, the usual culprit is detached mode swallowing the output. To debug:

1. Open **Settings > Scripts**.
2. Click **Test** next to your script. This always runs in capture mode and shows stdout, stderr, and the exit code in a popup.
3. Alternatively, toggle **Run detached** off for that script and trigger it normally.

Common issues:

- **Python or Node not installed** — `.py` and `.js` scripts will fail silently in detached mode if the runtime is missing. Test in capture mode to confirm.
- **Wrong path quoting** — always wrap `%1` (batch) or `$1` (shell/python) in double quotes so spaces in paths don't break the script.
- **PowerShell execution policy** — Windhover launches `.ps1` files with `-ExecutionPolicy Bypass`, so the usual "cannot be loaded because running scripts is disabled on this system" error does not apply here.

When testing a `.bat` file in non-detached mode, add `pause` at the end to keep the console window open long enough to read any output.

## Limitations

- Scripts run with your user's permissions. There is no sandbox — only run scripts you trust.
- Windhover does not elevate scripts. If your script needs admin rights, wrap it in a shortcut configured to **Run as administrator**, and point that shortcut at the script from outside Windhover.
- Detached scripts get no feedback from Windhover about success or failure — the exit code is not captured.
- Scripts cannot directly modify Windhover's SQLite database. If you need programmatic access to Windhover state, use the `window.__windhover` JavaScript API from a webview context instead (advanced).

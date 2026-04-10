# Windhover

A fast, local-first image manager built for power users. Browse, search, tag, and organize large image libraries with instant filtering and metadata analysis.

[![Latest Release](https://img.shields.io/github/v/release/UltraK18/Windhover?style=flat-square)](https://github.com/UltraK18/Windhover/releases/latest)
[![Downloads](https://img.shields.io/github/downloads/UltraK18/Windhover/total?style=flat-square)](https://github.com/UltraK18/Windhover/releases)
[![Platform](https://img.shields.io/badge/platform-Windows%20x64-blue?style=flat-square)]()
[![License](https://img.shields.io/badge/license-Proprietary-gray?style=flat-square)]()

---

## What is Windhover?

Windhover is a desktop image manager designed for people with large image collections. It combines the speed of a native app with the organizational power of a database-backed system.

Unlike cloud-based tools, everything runs locally. Your images stay on your machine, your data stays on your machine.

## Features

- **Instant search & filtering** — Full-text search across prompts, tag-based filtering with AND logic, model/LoRA/rating filters. All queries are index-backed for instant results.
- **Metadata extraction** — Automatically parses PNG metadata (A1111/Forge generation parameters, prompts, seeds, models, LoRAs). Non-destructive; original files are never modified.
- **Grid browser** — Eagle-style thumbnail grid with adjustable page sizes, infinite scroll mode, and folder/subfolder view with cover images.
- **Image viewer** — Full-screen viewer with zoom/pan, keyboard navigation, inline metadata panel, rating (0-5 stars), and favorites.
- **Collections** — Hierarchical collections with drag-and-drop, folder pull rules (auto-sync by tag filter), color-coded icons, and multi-image covers.
- **Tag system** — 55,000+ Danbooru tag reference with category-based coloring (general, artist, character, copyright, meta). Tag cloud sidebar, autocomplete search, clipboard copy.
- **Thumbnail caching** — On-demand JPEG thumbnail generation with MD5-bucketed cache. 4-thread parallel generation with epoch-based cancellation.
- **File watching** — Real-time detection of new images via filesystem watcher. Auto-indexing with configurable filters (exclude ADetailer, ControlNet intermediates).
- **User scripts** — Place `.bat`, `.ps1`, `.py`, or `.sh` files in the scripts folder. Bind keyboard shortcuts, access image metadata via environment variables. Open images in external editors with one keystroke.
- **System tray** — Minimize to tray, single-instance enforcement, restore from tray icon.
- **Privacy features** — Password lock screen (SHA-256), panic key (double-Esc), blur NSFW option.
- **Auto-update** — Checks for updates on startup and every 6 hours. One-click update with skip version option.
- **All image formats** — PNG, JPEG, WebP, GIF, BMP, TIFF, AVIF with animated format detection.
- **Keyboard-driven** — 16 remappable keybindings for viewer and grid navigation. Full keyboard workflow support.
- **Import** — Drag-and-drop files or paste URLs. Automatic metadata parsing on import.

## Installation

### Requirements
- Windows 10/11 (x64)
- ~50 MB disk space (plus thumbnail cache)

### Download
Download the latest MSI installer from the [Releases page](https://github.com/UltraK18/Windhover/releases/latest).

Run the installer — no dependencies or runtimes required. Updates are delivered automatically through the app.

### Updating
When a new version is available, a notification bar appears at the top of the app. Click **Update** to download and install automatically. You can also check manually in Settings > General > Check for Updates.

## Quick Start

1. **Add a folder** — Click the + button in the sidebar and select a folder containing images.
2. **Wait for scan** — Windhover indexes your images and extracts metadata. Progress is shown as a toast notification.
3. **Browse** — Use the grid to browse thumbnails. Click an image to open the full viewer.
4. **Search** — Type tags in the search bar. Use commas to add multiple tags (AND filter). Prefix with `n/` for negative tags.
5. **Organize** — Create collections, drag images into them, set up folder pull rules for automatic organization.

## User Scripts

Place script files in the scripts folder (Settings > Scripts > Open Folder):

```bat
@echo off
REM open-in-photoshop.bat
start "" "C:\Program Files\Adobe\Photoshop\photoshop.exe" "%1"
```

The image path is passed as the first argument. Additional metadata is available via environment variables:

| Variable | Description |
|----------|-------------|
| `WINDHOVER_FILE` | Full image path |
| `WINDHOVER_DIR` | Image directory |
| `WINDHOVER_TAGS` | Tags (comma-separated) |
| `WINDHOVER_MODEL` | Generation model |
| `WINDHOVER_SEED` | Seed value |

Assign keyboard shortcuts to scripts in Settings > Scripts.

## Tech Stack

Built with [Tauri v2](https://tauri.app/) (Rust backend) and [Svelte 5](https://svelte.dev/) (frontend). SQLite database with full-text search. All processing happens locally.

## Feedback & Issues

Report bugs or request features on the [Issues page](https://github.com/UltraK18/Windhover/issues).

---

*Windhover — because your images deserve better than a file explorer.*

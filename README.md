# Windhover

A fast, lightweight image manager for large libraries. Native speed, instant search, minimal footprint.

[![Latest Release](https://img.shields.io/github/v/release/UltraK18/Windhover?style=flat-square)](https://github.com/UltraK18/Windhover/releases/latest)
[![Downloads](https://img.shields.io/github/downloads/UltraK18/Windhover/total?style=flat-square)](https://github.com/UltraK18/Windhover/releases)
[![Platform](https://img.shields.io/badge/platform-Windows%20x64-blue?style=flat-square)]()
[![License](https://img.shields.io/badge/license-Freeware-green?style=flat-square)]()

---

## Why Windhover?

**Speed is the point.** Most image managers slow down as your library grows. Windhover doesn't. Every part of the app — search, filtering, grid rendering, viewer transitions — is engineered to stay instant, even with hundreds of thousands of images.

- **Native backend, not Electron.** Built on Rust, not a bundled browser. The installer is under 10 MB, RAM usage stays low, and startup is immediate.
- **Index-backed everything.** All queries hit a SQLite database with prepared statements. No full-text regex, no runtime string manipulation — every filter resolves through indexes.
- **Original files, untouched.** Windhover indexes your folders in place. No proprietary library format, no file copies, no lock-in.

## Features

**Browse & search**
- Grid browser with adjustable thumbnail sizes and infinite scroll for huge libraries
- Full-text search across prompts, tag-based filtering with AND logic, model/LoRA/rating filters
- Folder view with subfolder navigation and cover image previews
- Adaptive thumbnail resolution — 1280px when zoomed, 300px otherwise, only for visible images

**Image viewer**
- Full-screen viewer with zoom/pan and keyboard navigation
- Inline metadata panel showing prompts, generation parameters, tags, and LoRAs
- Rating (0-5 stars), favorites, custom user tags
- Live update modes: follow position or follow the image you're viewing

**Organization**
- Hierarchical collections with drag-and-drop and multi-image covers
- Folder pull rules — automatically sync images into collections by tag filter
- Custom user tags stored separately from parsed metadata tags
- Tag cloud sidebar with category-based coloring (55,000+ Danbooru tag reference)

**Metadata**
- Automatic PNG metadata extraction (A1111/Forge generation parameters, prompts, seeds, models, LoRAs)
- Tag parser handles weight syntax, LoRA references, and multi-tag weight groups
- Non-destructive — your original files are never modified

**Integration**
- User scripts — drop `.bat`, `.ps1`, `.py`, or `.sh` files into the scripts folder, bind keyboard shortcuts, open images in external editors
- File watcher — real-time detection of new images with auto-indexing
- Drag-and-drop import from file explorer or URLs

**Polish**
- System tray with minimize-to-tray, single instance
- Password lock screen, panic key, NSFW blur option
- Auto-update — checks every 6 hours, one-click install
- 16 remappable keyboard shortcuts
- All image formats — PNG, JPEG, WebP, GIF, BMP, TIFF, AVIF

## Installation

**Requirements**: Windows 10/11 (x64)

Download the latest MSI from the [Releases page](https://github.com/UltraK18/Windhover/releases/latest) and run it. No dependencies, no runtimes, no bundled browser.

Updates are delivered automatically through the app — click **Update** when the notification bar appears, or check manually in Settings.

## Quick Start

1. **Add a folder** — Click the + button in the sidebar and select a folder containing images.
2. **Browse** — Thumbnails load as the folder is indexed. Click any image to open the viewer.
3. **Search** — Type tags in the search bar, comma-separated for AND filter. Prefix with `n/` for negative tags.
4. **Organize** — Create collections and drag images in, or set up folder pull rules for automatic tag-based sorting.
5. **Customize** — Drop script files in Settings > Scripts to bind external tools to keyboard shortcuts.

## User Scripts

Place script files in the scripts folder (Settings > Scripts > Open Folder):

```bat
@echo off
REM open-in-photoshop.bat
start "" "C:\Program Files\Adobe\Photoshop\photoshop.exe" "%1"
```

The image path is passed as the first argument. Metadata is available via environment variables:

| Variable | Description |
|----------|-------------|
| `WINDHOVER_FILE` | Full image path |
| `WINDHOVER_DIR` | Image directory |
| `WINDHOVER_TAGS` | Tags (comma-separated) |
| `WINDHOVER_MODEL` | Generation model |
| `WINDHOVER_SEED` | Seed value |

Assign keyboard shortcuts to each script in Settings > Scripts.

## Tech Stack

Rust backend via [Tauri v2](https://tauri.app/). Svelte 5 frontend. SQLite with FTS5 for search. All processing happens locally; nothing is ever sent to a server.

## Feedback & Issues

Report bugs or request features on the [Issues page](https://github.com/UltraK18/Windhover/issues).

## License

Windhover is freeware. Free for personal and commercial use. Source code is not publicly available.

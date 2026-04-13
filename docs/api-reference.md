# Windhover API Reference

> Internal developer reference
> All commands are Tauri IPC calls via `invoke()`. Frontend wrappers are in `src/lib/api/commands.ts`.

---

## Types

### Folder

```typescript
interface Folder {
  id: number;
  path: string;
  label: string | null;
  isActive: boolean;
  sortOrder: number;
  createdAt: string | null;
  lastScannedAt: string | null;
  filterAllImages: boolean;
  excludeAdBefore: boolean;
  excludeControlnet: boolean;
  isSystem: boolean;
}
```

### ImageSummary

Lightweight image record used in grid views.

```typescript
interface ImageSummary {
  id: number;
  filename: string;
  relativePath: string;
  folderId: number;
  width: number | null;
  height: number | null;
  model: string | null;
  rating: number;
  isFavorite: boolean;
  thumbnailPath: string | null;
}
```

### ImageRow

Full image record with all metadata fields.

```typescript
interface ImageRow {
  id: number;
  folderId: number;
  relativePath: string;
  filename: string;
  fileSize: number | null;
  fileModifiedAt: string | null;
  positivePrompt: string;
  negativePrompt: string;
  steps: number | null;
  sampler: string | null;
  scheduleType: string | null;
  cfgScale: number | null;
  seed: number | null;
  width: number | null;
  height: number | null;
  model: string | null;
  modelHash: string | null;
  denoisingStrength: number | null;
  clipSkip: number | null;
  rating: number;
  isFavorite: boolean;
  userNote: string | null;
  thumbnailPath: string | null;
  thumbnailGenerated: boolean;
  createdAt: string | null;
  sourceUrl: string | null;
  deletedAt: string | null;
}
```

### ImageFilter

All fields are optional. Combine for AND logic.

```typescript
interface ImageFilter {
  folder_id?: number;
  collection_id?: number;
  model?: string;
  min_rating?: number;
  is_favorite?: boolean;
  tags?: string[];
  lora?: string;
  search_query?: string;
  negative_tags?: string[];
  include_deleted?: boolean;
  subdir?: string;
  subdir_exact?: boolean;
}
```

### SortBy

```typescript
type SortBy = "CreatedDesc" | "CreatedAsc" | "RatingDesc" | "FilenameAsc" | "SeedAsc";
```

### TagRow

```typescript
interface TagRow {
  id: number;
  name: string;
  category: string;
  useCount: number;
}
```

### LoraRow

```typescript
interface LoraRow {
  id: number;
  name: string;
  displayName: string | null;
  useCount: number;
}
```

### Collection

```typescript
interface Collection {
  id: number;
  name: string;
  parentId: number | null;
  coverImageId: number | null;
  sortOrder: number;
  createdAt: string | null;
  color: string | null;
  imageCount: number;
  childCount: number;
  coverImageIds: number[];
  coverThumbnails: string[];
}
```

### CollectionSource

```typescript
interface CollectionSource {
  id: number;
  collectionId: number;
  folderId: number;
  tagFilter: string[];
  syncMode: string;
  createdAt: string | null;
}
```

### SubdirInfo

```typescript
interface SubdirInfo {
  name: string;
  fullPath: string;
  imageCount: number;
  coverImageIds: number[];
  coverThumbnails: string[];
}
```

### SubdirPage

```typescript
interface SubdirPage {
  items: SubdirInfo[];
  total: number;
}
```

### ScanResult

```typescript
interface ScanResult {
  totalFiles: number;
  newFiles: number;
  changedFiles: number;
  deletedFiles: number;
  skippedFiles: number;
  elapsedMs: number;
}
```

---

## Commands

### Folders

#### addFolder

`invoke("add_folder", { path })`

**Params:** `path: string` — absolute path to the folder
**Returns:** `number` — the new folder's ID

Registers a folder for scanning and browsing.

#### removeFolder

`invoke("remove_folder", { folderId })`

**Params:** `folderId: number`
**Returns:** `void`

Unregisters a folder and removes its images from the database.

#### getFolders

`invoke("get_folders")`

**Params:** none
**Returns:** `Folder[]`

Returns all registered folders ordered by `sortOrder`.

#### reorderFolders

`invoke("reorder_folders", { folderIds })`

**Params:** `folderIds: number[]` — folder IDs in the desired order
**Returns:** `void`

Sets the display order of folders in the sidebar.

#### updateFolderSettings

`invoke("update_folder_settings", { folderId, filterAllImages, excludeAdBefore, excludeControlnet })`

**Params:** `folderId: number`, `filterAllImages: boolean`, `excludeAdBefore: boolean`, `excludeControlnet: boolean`
**Returns:** `void`

Updates per-folder filter flags.

#### getSubdirs

`invoke("get_subdirs", { folderId, subdir, limit, offset, sort })`

**Params:** `folderId: number`, `subdir?: string`, `limit?: number`, `offset?: number`, `sort?: string`
**Returns:** `SubdirPage`

Returns paginated subdirectory listing with image counts and cover thumbnails.

---

### Scanning

#### startScan

`invoke("start_scan", { folderId, fullRescan })`

**Params:** `folderId: number`, `fullRescan?: boolean`
**Returns:** `ScanResult`

Runs an incremental (or full) scan of the folder. Async operation that emits `scan:progress` and `scan:complete` events during execution.

---

### Images

#### getImages

`invoke("get_images", { filter, sort, page, pageSize })`

**Params:** `filter: ImageFilter`, `sort: SortBy`, `page: number`, `pageSize: number`
**Returns:** `ImageSummary[]`

Fetches a page of images matching the filter criteria.

#### getImageDetail

`invoke("get_image_detail", { imageId })`

**Params:** `imageId: number`
**Returns:** `ImageRow | null`

Returns full metadata for a single image, or null if not found.

#### getImageCount

`invoke("get_image_count", { filter })`

**Params:** `filter: ImageFilter`
**Returns:** `number`

Returns the total count of images matching the filter.

#### getImageFullPath

`invoke("get_image_full_path", { imageId })`

**Params:** `imageId: number`
**Returns:** `string`

Resolves the absolute filesystem path for an image.

#### getImageFullPaths

`invoke("get_image_full_paths", { imageIds })`

**Params:** `imageIds: number[]`
**Returns:** `string[]`

Batch version of `getImageFullPath`.

#### softDeleteImages

`invoke("soft_delete_images", { imageIds })`

**Params:** `imageIds: number[]`
**Returns:** `void`

Marks images as deleted without removing files.

#### restoreImages

`invoke("restore_images", { imageIds })`

**Params:** `imageIds: number[]`
**Returns:** `void`

Restores soft-deleted images.

#### permanentDeleteImages

`invoke("permanent_delete_images", { imageIds })`

**Params:** `imageIds: number[]`
**Returns:** `void`

Permanently removes images and their files from disk.

#### setSourceUrl

`invoke("set_source_url", { imageId, sourceUrl })`

**Params:** `imageId: number`, `sourceUrl: string`
**Returns:** `void`

Sets the source URL for an image (e.g., Danbooru post URL, Pixiv artwork URL).

#### revealPathInExplorer

`invoke("reveal_path_in_explorer", { path })`

**Params:** `path: string`
**Returns:** `void`

Opens the system file explorer and highlights the given file or folder.

---

### Tags

#### getTagCloud

`invoke("get_tag_cloud", { limit, category })`

**Params:** `limit: number`, `category?: string`
**Returns:** `TagRow[]`

Returns the most-used tags, optionally filtered by category.

#### autocompleteTag

`invoke("autocomplete_tag", { prefix, limit })`

**Params:** `prefix: string`, `limit: number`
**Returns:** `TagRow[]`

Returns tags matching the given prefix for search autocomplete.

#### getImageTags

`invoke("get_image_tags", { imageId })`

**Params:** `imageId: number`
**Returns:** `[string, number][]`

Returns tag name and category pairs for an image.

#### rebuildTags

`invoke("rebuild_tags")`

**Params:** none
**Returns:** `number`

Re-parses all images and rebuilds the tags table. Returns the number of tags processed.

---

### Custom Tags

User-defined tags stored separately from auto-parsed metadata tags. Survive Rebuild Tags. Searchable alongside regular tags.

#### getImageCustomTags

`invoke("get_image_custom_tags", { imageId })`

**Params:** `imageId: number`
**Returns:** `string[]`

Get custom tags for a specific image.

#### addCustomTag

`invoke("add_custom_tag", { imageId, tagName })`

**Params:** `imageId: number`, `tagName: string`

Add a custom tag to an image. Creates the tag if it doesn't exist.

#### removeCustomTag

`invoke("remove_custom_tag", { imageId, tagName })`

**Params:** `imageId: number`, `tagName: string`

Remove a custom tag from an image. Cleans up orphaned tags.

#### addCustomTagBatch

`invoke("add_custom_tag_batch", { imageIds, tagName })`

**Params:** `imageIds: number[]`, `tagName: string`

Add the same custom tag to multiple images at once.

#### autocompleteCustomTag

`invoke("autocomplete_custom_tag", { prefix, limit })`

**Params:** `prefix: string`, `limit: number`
**Returns:** `string[]`

Autocomplete custom tag names by prefix.

---

### LoRA

#### getLoras

`invoke("get_loras", { sortByCount })`

**Params:** `sortByCount?: boolean`
**Returns:** `LoraRow[]`

Returns all LoRAs found across indexed images.

#### getImageLoras

`invoke("get_image_loras", { imageId })`

**Params:** `imageId: number`
**Returns:** `[string, number][]`

Returns LoRA name and weight pairs for an image.

---

### Rating

#### setRating

`invoke("set_rating", { imageId, rating })`

**Params:** `imageId: number`, `rating: number`
**Returns:** `void`

Sets the rating (0-5) for an image.

#### toggleFavorite

`invoke("toggle_favorite", { imageId })`

**Params:** `imageId: number`
**Returns:** `boolean`

Toggles the favorite flag. Returns the new state.

---

### Thumbnails

#### getThumbnailUrl

`invoke("get_thumbnail_url", { imageId, size })`

**Params:** `imageId: number`, `size?: number`
**Returns:** `string`

Returns the asset protocol URL for a cached thumbnail, generating it if needed.

#### requestThumbnails

`invoke("request_thumbnails", { imageIds, priority, size })`

**Params:** `imageIds: number[]`, `priority: boolean`, `size?: number`
**Returns:** `void`

Queues batch thumbnail generation. Results arrive via `thumbnail:ready` events.

#### cancelThumbnails

`invoke("cancel_thumbnails")`

**Params:** none
**Returns:** `void`

Cancels pending thumbnail generation by incrementing the epoch counter.

---

### Preferences

#### getPreference

`invoke("get_preference", { key })`

**Params:** `key: string`
**Returns:** `unknown | null`

Retrieves a single preference value by key.

#### getAllPreferences

`invoke("get_all_preferences")`

**Params:** none
**Returns:** `Record<string, unknown>`

Returns all stored preferences as a key-value map.

#### setPreference

`invoke("set_preference", { key, value })`

**Params:** `key: string`, `value: unknown`
**Returns:** `void`

Stores a preference value (upsert).

#### deletePreference

`invoke("delete_preference", { key })`

**Params:** `key: string`
**Returns:** `void`

Removes a preference entry.

---

### Collections

#### createCollection

`invoke("create_collection", { name, parentId })`

**Params:** `name: string`, `parentId?: number`
**Returns:** `number`

Creates a new collection. Returns the new collection's ID.

#### renameCollection

`invoke("rename_collection", { collectionId, name })`

**Params:** `collectionId: number`, `name: string`
**Returns:** `void`

Renames an existing collection.

#### deleteCollection

`invoke("delete_collection", { collectionId })`

**Params:** `collectionId: number`
**Returns:** `void`

Deletes a collection and its image associations (not the images themselves).

#### moveCollection

`invoke("move_collection", { collectionId, newParentId })`

**Params:** `collectionId: number`, `newParentId?: number`
**Returns:** `void`

Moves a collection under a new parent, or to root if `newParentId` is omitted.

#### setCollectionCover

`invoke("set_collection_cover", { collectionId, imageId })`

**Params:** `collectionId: number`, `imageId?: number`
**Returns:** `void`

Sets or clears the cover image for a collection.

#### setCollectionColor

`invoke("set_collection_color", { collectionId, color })`

**Params:** `collectionId: number`, `color?: string`
**Returns:** `void`

Sets or clears the accent color for a collection.

#### getCollections

`invoke("get_collections", { parentId })`

**Params:** `parentId?: number`
**Returns:** `Collection[]`

Returns child collections under the given parent (or root collections if omitted).

#### getCollectionPath

`invoke("get_collection_path", { collectionId })`

**Params:** `collectionId: number`
**Returns:** `Collection[]`

Returns the ancestor chain from root to the given collection (breadcrumb).

#### addImagesToCollection

`invoke("add_images_to_collection", { collectionId, imageIds })`

**Params:** `collectionId: number`, `imageIds: number[]`
**Returns:** `void`

Links images to a collection.

#### removeImagesFromCollection

`invoke("remove_images_from_collection", { collectionId, imageIds })`

**Params:** `collectionId: number`, `imageIds: number[]`
**Returns:** `void`

Unlinks images from a collection.

#### getImageCollections

`invoke("get_image_collections", { imageId })`

**Params:** `imageId: number`
**Returns:** `Collection[]`

Returns all collections that contain the given image.

#### reorderCollections

`invoke("reorder_collections", { collectionIds })`

**Params:** `collectionIds: number[]`
**Returns:** `void`

Sets the display order of sibling collections.

---

### Collection Sources

#### addCollectionSource

`invoke("add_collection_source", { collectionId, folderId, tagFilter, syncMode })`

**Params:** `collectionId: number`, `folderId: number`, `tagFilter: string[]`, `syncMode: string`
**Returns:** `number`

Creates a source rule linking a folder's tag-filtered images to a collection. Returns the source ID.

#### updateCollectionSource

`invoke("update_collection_source", { sourceId, tagFilter, syncMode })`

**Params:** `sourceId: number`, `tagFilter: string[]`, `syncMode: string`
**Returns:** `void`

Updates the filter and sync mode for an existing source.

#### removeCollectionSource

`invoke("remove_collection_source", { sourceId })`

**Params:** `sourceId: number`
**Returns:** `void`

Deletes a collection source rule.

#### getCollectionSources

`invoke("get_collection_sources", { collectionId })`

**Params:** `collectionId: number`
**Returns:** `CollectionSource[]`

Returns all source rules for a collection.

#### executePull

`invoke("execute_pull", { sourceId })`

**Params:** `sourceId: number`
**Returns:** `number`

Executes the source rule, syncing matching images into the collection. Returns the number of images added.

---

### Import

#### importFiles

`invoke("import_files", { filePaths, targetFolderId, collectionId })`

**Params:** `filePaths: string[]`, `targetFolderId?: number`, `collectionId?: number`
**Returns:** `number[]`

Imports local files into the database. If `targetFolderId` is specified, copies files to that folder; otherwise uses the system imports folder. Optionally links to a collection. Returns the IDs of imported images.

#### importUrl

`invoke("import_url", { url, targetFolderId, collectionId })`

**Params:** `url: string`, `targetFolderId?: number`, `collectionId?: number`
**Returns:** `number[]`

Downloads an image from a URL and imports it. Same target logic as `importFiles`. Returns the IDs of imported images.

---

## Scripts

> For user-facing documentation on writing scripts, see [docs/scripts.md](./scripts.md).

File-based user script system. Scripts are placed in `{appDataDir}/scripts/` folder and auto-detected. Supported extensions: `.bat`, `.cmd`, `.ps1`, `.py`, `.sh`, `.js`. Per-script settings (shortcut, context menu, detached mode) stored in preferences.

### Types

```typescript
interface ScriptInfo {
  filename: string;        // e.g. "open-photoshop.bat"
  name: string;            // e.g. "open photoshop" (derived from filename)
  path: string;            // Full path to script file
  ext: string;             // e.g. "bat"
  shortcut: { key: string; ctrl?: boolean; shift?: boolean; alt?: boolean } | null;
  show_in_context_menu: boolean;
  run_detached: boolean;
}

interface ScriptSettings {
  shortcut?: { key: string; ctrl?: boolean; shift?: boolean; alt?: boolean } | null;
  show_in_context_menu: boolean;
  run_detached: boolean;
}

interface ScriptResult {
  success: boolean;
  stdout: string;
  stderr: string;
  exit_code: number | null;
}
```

### Script Arguments & Environment Variables

Scripts receive the primary image path as the first argument (`%1` in .bat, `$1` in .sh/.py). Metadata is passed via environment variables:

| Env Variable | Description |
|-------------|-------------|
| `WINDHOVER_FILE` | Full image path |
| `WINDHOVER_DIR` | Image directory |
| `WINDHOVER_FILENAME` | Filename with extension |
| `WINDHOVER_NAME` | Filename without extension |
| `WINDHOVER_EXT` | Extension |
| `WINDHOVER_SELECTION` | All selected paths (newline-separated) |
| `WINDHOVER_COUNT` | Number of selected images |
| `WINDHOVER_MODEL` | Generation model |
| `WINDHOVER_SEED` | Seed value |
| `WINDHOVER_RATING` | Star rating (0-5) |
| `WINDHOVER_TAGS` | Tags (comma-separated) |
| `WINDHOVER_WIDTH` | Image width |
| `WINDHOVER_HEIGHT` | Image height |
| `WINDHOVER_PROMPT` | Positive prompt text |

### Example Script (open-in-photoshop.bat)

```bat
@echo off
start "" "C:\Program Files\Adobe\Photoshop\photoshop.exe" "%1"
```

### Commands

#### getScripts

`invoke("get_scripts")`

**Returns:** `ScriptInfo[]`

Scan scripts directory and return all detected scripts with their settings.

#### saveScripts

`invoke("save_scripts", { settings })`

**Params:** `settings: Record<string, ScriptSettings>` (keyed by filename)

Save per-script settings (shortcut, context menu, detached mode).

#### getScriptsDirPath

`invoke("get_scripts_dir_path")`

**Returns:** `string`

Get the scripts directory path.

#### openScriptsDir

`invoke("open_scripts_dir")`

**Params:** none
**Returns:** `void`

Opens the scripts directory in the system file explorer.

#### executeScript

`invoke("execute_script", { filename, imageIds, captureOverride })`

**Params:** `filename: string`, `imageIds: number[]`, `captureOverride?: boolean | null`

**Returns:** `ScriptResult`

Execute a script file with image context. If `run_detached` is true, spawns and returns immediately. Otherwise captures stdout/stderr.

When `captureOverride` is set to `true`, the script always runs in capture mode (waiting for completion and collecting stdout/stderr) regardless of the script's `run_detached` setting. This is used by the **Test** button in Settings > Scripts. With `captureOverride = true`, `imageIds` may also be empty — the script runs without an image context and `WINDHOVER_*` environment variables are left unset.

#### createSampleScripts

`invoke("create_sample_scripts")`

**Params:** none
**Returns:** `string[]` — filenames of newly created sample scripts (empty if all samples already exist)

Writes a set of ready-to-use sample scripts into the scripts folder. Existing files are not overwritten. Called by the **Add sample scripts** button in Settings > Scripts.

---

## Events

| Event | Payload | Description |
|-------|---------|-------------|
| `scan:progress` | `string` | Progress message during folder scanning |
| `scan:complete` | `string` | Completion message when scan finishes |
| `thumbnail:ready` | `{ imageId: number, thumbUrl: string }` | Emitted when a thumbnail has been generated and cached |
| `image:added` | `number` | Count of images added (emitted after watcher detects new files) |
| `import:progress` | `{ current: number, total: number, progress: number, filename: string }` | Progress update during file/URL import |
| `import:complete` | `{ count: number }` | Emitted when import operation finishes |

---

## Notes

- **Tag equivalence:** Spaces and underscores are treated equivalently in tag filters (e.g., `blue_eyes` matches `blue eyes`).
- **Tag categories:** `0` = general (blue), `1` = artist (orange), `3` = copyright (purple), `4` = character (orange), `5` = meta (green), `-1` = unregistered (gray).
- **Pagination:** Page index is 0-based.
- **SortBy:** String enum values passed directly (e.g., `"CreatedDesc"`).
- **ImageFilter:** All fields are optional. When multiple fields are set, they combine with AND logic.
- **Negative tags in sources:** Prefix tag names with `n/` in collection source `tagFilter` arrays to exclude images with that tag.

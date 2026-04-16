# Known Issues — Home Library Tracker

Tracked incomplete features, missing functionality, and tech debt. Update this file after each work session. When an issue is resolved, move it to `memory/changelog.md` and remove it from this file.

---

## 🔴 Incomplete Features

### Barcode Detection (BarcodeScanner.tsx)

**Status:** INCOMPLETE — UI stub only, no decode library integrated.

`BarcodeScanner.tsx` includes:
- Camera permission request and video feed UI
- Manual barcode/ISBN entry fallback input
- `onScanComplete(barcode: string)` callback wired to the parent

**What is missing:**
- A decode library to read barcodes from the video stream. Recommended options:
  - `@zxing/browser` — well-maintained, supports 1D and 2D codes including ISBN barcodes (EAN-13)
  - `quagga2` — alternative, good for 1D codes
- Enrichment API integration: once a barcode is decoded, the app should look up book/media metadata. Candidate APIs:
  - **Open Library** — `https://openlibrary.org/api/books?bibkeys=ISBN:${barcode}&format=json&jscmd=data` (free, no key)
  - **Google Books** — `https://www.googleapis.com/books/v1/volumes?q=isbn:${barcode}` (free tier)
  - **UPC Item DB** — `https://api.upcitemdb.com/prod/trial/lookup?upc=${barcode}` (useful for non-book media)
  - **Discogs** — for vinyl records (requires API key)

**Implementation guide:** See `BARCODE_IMPLEMENTATION.md` in `home-library-tracker` for the full step-by-step.
**Skill file:** `skills/complete-barcode-scanner.md` contains a ready-to-fire agent prompt.

---

## 🟡 Missing Features

### Import / Export

**Status:** MISSING — no JSON or CSV export implemented.

The KV store holds `LibraryItem[]` under the key `'library-items'`. Exporting is straightforward: read the KV value and serialise to JSON or convert to CSV rows. There is currently no export button, no import flow, and no file-picker UI for importing a backup.

**Suggested scope:**
- Export: download button in settings or toolbar → `JSON.stringify(items)` → `Blob` → anchor download
- Import: file input → `JSON.parse` → validate shape → merge or replace KV value

---

### Sorting

**Status:** MISSING — items display in insertion order only.

There are no sort controls. Items cannot be sorted by title, author, date added, media type, or condition. The `filteredItems` memo only filters; it does not sort.

**Suggested scope:**
- Add a `sortBy` state (`'title' | 'dateAdded' | 'mediaType' | 'condition'`) and `sortDir` (`'asc' | 'desc'`)
- Extend the `filteredItems` memo to sort after filtering
- Add sort controls to the filter panel or toolbar

---

### Bulk Operations

**Status:** MISSING — no multi-select, bulk delete, or bulk location update.

All operations (edit, delete, location change) are single-item only. There is no checkbox-based multi-select and no toolbar for acting on a selection.

**Suggested scope:**
- Add `selectedIds: Set<string>` state and a toggle-select mode
- Bulk delete: remove all items in `selectedIds` from KV in a single write
- Bulk location update: set `location` on all selected items in a single write
- Consider a "select all" shortcut for filtered results

---

## 🔵 Tech Debt

### App.tsx is too large

**Status:** TECH DEBT — multiple distinct concerns co-located in a single component.

`App.tsx` currently contains:
- Filter state (`selectedMediaTypes`, `selectedLocations`, `selectedConditions`, `searchQuery`)
- `filteredItems` memo
- Dialog state (`dialogMode`, `editingItem`, `scannedBarcode`, `deletingItem`)
- Stats computation
- Top-level render tree

**Suggested refactor — extraction candidates:**

| Hook | Responsibility |
|---|---|
| `useLibraryFilters` | `selectedMediaTypes`, `selectedLocations`, `selectedConditions`, `searchQuery`, `filteredItems` memo |
| `useLibraryStats` | Derives stats (total items, by type, wishlist count, etc.) from `items` |
| `useDialogState` | `dialogMode`, `editingItem`, `scannedBarcode`, `deletingItem`, open/close handlers |

**Reference:** `prompts/start-refactor-session.md` contains a pre-built agent prompt for this refactor.

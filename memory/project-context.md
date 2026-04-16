# Project Context: Library Keeper (home-library-tracker)

Persistent context about the current state of the `home-library-tracker` project. Update this file whenever significant changes are made to the app.

---

## Project Purpose

**Library Keeper** is a personal media collection manager built as a **GitHub Spark** app. It helps a single user track their physical media — books, vinyl records, CDs, DVDs, and games — across different locations (e.g. home shelf, storage, loaned out) and conditions. It also includes a wishlist tab for items the user wants to acquire.

The app is entirely client-side with no backend. All persistence uses GitHub Spark's `useKV` hook.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | React 18 + TypeScript (strict) |
| Persistence | GitHub Spark `useKV` hook |
| Styling | Tailwind CSS v4 |
| UI Components | shadcn/ui (Radix primitives) |
| Icons | Phosphor Icons (`@phosphor-icons/react`) |
| Animations | Framer Motion (`AnimatePresence`, `motion.*`) |
| Notifications | sonner (`toast`) |
| Build | Vite + `@github/spark` plugin |
| Routing | None — single-page app |

---

## Domain Types

Defined in **`src/lib/types.ts`** (always read this file before making type changes).

```ts
type MediaType = 'book' | 'vinyl' | 'cd' | 'dvd' | 'game';
type ItemLocation = 'shelf' | 'storage' | 'loaned' | 'digital' | 'other';
type ItemCondition = 'mint' | 'good' | 'fair' | 'poor';

interface LibraryItem {
  id: string;
  title: string;
  mediaType: MediaType;
  // additional fields — see src/lib/types.ts for full current shape
}
```

---

## Current Feature Set

| Feature | Status | Notes |
|---|---|---|
| Add item | ✅ Complete | Via `ItemForm` component |
| Edit item | ✅ Complete | Re-uses `ItemForm` in edit mode |
| Delete item | ✅ Complete | With confirmation |
| View collection | ✅ Complete | Grid/list of `LibraryItemCard` components |
| Filter by media type | ✅ Complete | Tabs or filter buttons |
| Filter by location | ✅ Complete | Dropdown filter |
| Filter by condition | ✅ Complete | Dropdown filter |
| Wishlist tab | ✅ Complete | Separate tab; items marked `isWishlisted: true` |
| Collection stats | ✅ Complete | Count by media type |
| Barcode scanning | ⚠️ Partial | UI exists; needs real scanning library — see `BARCODE_IMPLEMENTATION.md` |
| Import / Export | ❌ Not started | No import/export of collection data yet |
| Sorting | ❌ Not started | Items not yet sortable by title, date added, etc. |
| Cover art | ❌ Not started | No cover image support |
| Multi-user | ❌ Out of scope | Single-user personal tool |

---

## Known Gaps

1. **Barcode scanning** — The `BarcodeScanner` component exists and the UI is in place, but it does not yet use a real barcode scanning library. A real implementation needs either `zxing-js/library` or `quagga2`. See `BARCODE_IMPLEMENTATION.md` for the plan.

2. **No import/export** — Users cannot currently export their library to JSON/CSV or import from another source. This is a high-value feature for data portability.

3. **No sorting** — Items are displayed in insertion order. There is no UI to sort by title, date added, media type, or condition.

4. **No cover art** — Items have no cover image. Adding this would require either a URL field on `LibraryItem` or integration with an external book/music API.

5. **No search** — There is no text search across item titles/authors. Filtering by type/location/condition is available but not free-text search.

---

## Key Files to Always Read Before Making Changes

| File | Why |
|---|---|
| `src/lib/types.ts` | All domain types — the foundation of everything |
| `src/App.tsx` | Root component — KV keys, top-level state, component composition |
| `PRD.md` | Product requirements — what's planned and why |
| `BARCODE_IMPLEMENTATION.md` | Notes on completing barcode scanning |

---

## KV Keys in Use

Defined in `src/lib/constants.ts` (check this file for the current list):

| Key | Type | Purpose |
|---|---|---|
| `'library-items'` | `LibraryItem[]` | Main collection |
| `'wishlist-items'` | `LibraryItem[]` | Wishlist (or a flag on `LibraryItem`) |
| `'user-preferences'` | `UserPreferences` | Theme, default view, etc. |

---

## Architecture Notes

- **No routing** — the app is a single page. Tab switching is managed with local React state.
- **Top-level state** — most KV state lives in `src/App.tsx` and is passed down as props. If prop drilling exceeds 2 levels, consider a context.
- **Component library** — shadcn/ui components are installed into `src/components/ui/`. Do not modify these files directly; instead use the `className` prop to customise.
- **Animations** — list transitions use `AnimatePresence` + `motion.div`. Do not remove these when refactoring list components.

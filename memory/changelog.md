# Changelog — Home Library Tracker

## Maintenance instructions

Append a new section after each work session using the format below. Keep entries in reverse-chronological order (newest at top). When a known issue from `memory/known-issues.md` is resolved, note it here and remove it from that file.

```markdown
## YYYY-MM-DD — Title

**Features / changes:**
- ...

**Tech:**
- ...

**Resolved issues:**
- ...

**New known gaps (add to known-issues.md):**
- ...
```

---

## 2026-04-16 — Initial scaffold

**Features:**
- Add / edit / delete `LibraryItem` with full form (title, creator, media type, location, condition, notes, ISBN/UPC, cover image URL, wishlist flag)
- 5 media types: book, vinyl, CD, DVD, video game
- 5 location options (configurable shelf/room labels)
- 5 condition grades (Mint → Poor)
- Wishlist tab — separate view of items where `isWishlist: true`
- Filter panel: filter by media type, location, and condition (multi-select)
- Full-text search across title, creator, and notes fields
- Stats dashboard: total items, breakdown by media type, wishlist count
- Barcode scanner UI stub with camera permission request, video feed placeholder, and manual entry fallback input
- Responsive mobile-first layout (single-column on small screens, grid on md+)

**Tech:**
- Spark KV key `'library-items'` stores `LibraryItem[]`
- UI components: shadcn/ui (Button, Dialog, Input, Select, Badge, Tabs, etc.)
- Icons: Phosphor Icons via `@phosphor-icons/react`
- Animations: Framer Motion `AnimatePresence` for list enter/exit transitions
- Toast notifications: Sonner
- Fonts: Crimson Pro (headings) + Inter (body) via Google Fonts
- Color palette: burgundy (`oklch(0.42 0.12 15)`), amber (`oklch(0.68 0.18 55)`), paper background (`oklch(0.97 0.012 70)`)

**Known gaps (tracked in known-issues.md):**
- No barcode detection library integrated — `BarcodeScanner.tsx` is a UI stub only
- No import / export functionality
- No sorting controls — items display in insertion order
- No bulk operations (multi-select, bulk delete, bulk location update)

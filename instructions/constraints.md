# Constraints — Home Library Tracker

Hard rules that agents **must never break** when working on `home-library-tracker`. Each rule is load-bearing; violating any one of them can silently corrupt data, break the UI, or introduce style inconsistencies that are hard to reverse.

---

## 1. Never change `LibraryItem` shape without a KV migration plan

**Rule:** Do not add, remove, or rename fields on `LibraryItem` unless you also write a migration that reads the existing `'library-items'` KV value and transforms every stored item to the new shape before writing it back.

**Rationale:** The Spark KV store persists `LibraryItem[]` as a JSON blob under the key `'library-items'`. There is no schema version or automatic migration. If a field is renamed or removed, existing items silently lose data; if a new required field is added without a default, existing items will be `undefined` for that field and runtime errors or blank UI will follow.

---

## 2. Never replace `useKV` with `useState` or `localStorage` for persistent data

**Rule:** All data that must survive a page refresh — primarily the `library-items` collection — must use the Spark `useKV` hook. Do not swap it for `useState`, `useReducer`, `localStorage`, `sessionStorage`, or any other client-side mechanism.

**Rationale:** `useKV` is the Spark platform's durable storage layer. It persists across sessions and devices for the authenticated user. `useState` is ephemeral; `localStorage` is not available in all Spark environments and is not synced. Switching silently discards the user's entire library on reload.

---

## 3. Never remove `BarcodeScanner` component or its UI

**Rule:** Do not delete, disable, or hide the `BarcodeScanner` component or its manual-entry fallback input. The barcode detection library is intentionally not yet wired up (it is a stub), but the UI shell and manual fallback must remain in place.

**Rationale:** The camera UI and manual-entry path are the designed UX surface for barcode input. Removing them forces users to type ISBNs/UPCs entirely by hand with no future upgrade path. The stub is a deliberate placeholder — completing it is a tracked improvement, not a reason to remove it.

---

## 4. Never remove the `isWishlist` flag from `LibraryItem`

**Rule:** The `isWishlist: boolean` field on `LibraryItem` must not be removed, renamed, or repurposed.

**Rationale:** `isWishlist` is the single source of truth that drives the Collection / Wishlist tab system in the main UI. Every filter, stat, and display path branches on this flag. Removing it collapses the two-tab system and corrupts the meaning of every stored item that was saved as a wishlist entry.

---

## 5. Never add a client-side router without a KV key and deep-link strategy

**Rule:** Do not introduce React Router, TanStack Router, or any history-based routing until you have also designed: (a) which routes exist, (b) whether any route needs to hydrate state from a KV key, and (c) how deep-links behave for unauthenticated or empty-library states.

**Rationale:** Spark apps are single-page by default. Adding a router without a matching KV/state strategy produces broken deep-links, double-fetches on navigation, and flash-of-empty-content on load. Routing is a significant architectural change that must be planned end-to-end.

---

## 6. Never use Lucide or Heroicons — Phosphor Icons only

**Rule:** All icons must come from `@phosphor-icons/react`. Do not import from `lucide-react`, `@heroicons/react`, or any other icon library.

**Rationale:** The design system uses Phosphor Icons exclusively. Mixing icon libraries produces visual inconsistency (different stroke weights, optical sizes, and metaphors). `@phosphor-icons/react` is already installed; adding another icon library adds bundle weight and drift.

---

## 7. Never write inline styles — Tailwind utilities only; CSS vars only in designated files

**Rule:** Do not use the `style` prop for visual styling. All styles must be expressed as Tailwind utility classes. CSS custom properties (variables) may only be defined in `src/index.css` or `src/styles/theme.css`.

**Rationale:** Inline styles bypass Tailwind's responsive and dark-mode modifiers, cannot be overridden by utility classes, and are invisible to design-system audits. Centralising CSS vars in the theme files keeps the color/spacing palette in one authoritative location and makes global changes safe.

---

## 8. Never recreate shadcn/ui components that already exist in `src/components/ui/`

**Rule:** Before building a new UI primitive (button, dialog, badge, input, select, etc.), check `src/components/ui/`. If the component exists there, use or extend it — do not write a parallel implementation.

**Rationale:** `src/components/ui/` contains the project's shadcn/ui component library, configured with the correct variant tokens and theme. Duplicating these components creates two sources of truth, diverging styles, and doubled maintenance burden. shadcn/ui components are designed to be extended via the `cn` utility and `variants` API.

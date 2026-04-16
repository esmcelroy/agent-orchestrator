# GitHub Spark Coding Standards

Standards for using GitHub Spark APIs and conventions in `home-library-tracker`.

---

## The `useKV` Hook

`useKV` is the **only** mechanism for persistent state in a Spark app. There is no backend, no database, and no server — all data lives in the Spark KV store.

### Import

```ts
import { useKV } from "@github/spark/hooks";
```

### Usage

```ts
const [items, setItems] = useKV<LibraryItem[]>('library-items', []);
```

- The first argument is the **KV key** (see naming rules below).
- The second argument is the **default value** used when the key has not been set yet.
- Always provide an explicit type parameter `<T>` — never let TypeScript infer `unknown` or `any`.
- `setItems` behaves like React's `setState` setter — it accepts a value or an updater function.

---

## KV Key Naming

- All KV keys must be **namespaced with a project-relevant prefix** using kebab-case.
- Use the format `'<namespace>-<resource>'`.

| Resource | Key |
|---|---|
| Library items collection | `'library-items'` |
| User preferences | `'user-preferences'` |
| Wishlist items | `'wishlist-items'` |
| Filter state | `'active-filters'` |
| UI settings | `'ui-settings'` |

- **Do not** use generic keys like `'data'`, `'items'`, or `'state'` — they will collide if the app ever gains multi-user or multi-collection support.
- Define all KV keys as **constants** in `src/lib/constants.ts`:

```ts
// src/lib/constants.ts
export const KV_KEYS = {
  LIBRARY_ITEMS: 'library-items',
  USER_PREFERENCES: 'user-preferences',
  WISHLIST_ITEMS: 'wishlist-items',
} as const;
```

---

## Storage Rules

- **Never use `localStorage` or `sessionStorage` directly.** Spark KV is the correct abstraction.
- **Never use `IndexedDB`, cookies, or any other browser storage API.**
- Do not store derived data in KV — derive it at runtime from the source KV values using `useMemo`.

---

## Data Size

- KV values have size limits. Avoid storing large blobs (e.g. base64-encoded images) in KV.
- If a collection could grow large, **split data across multiple keys** rather than storing one giant array.
  - Example: store item metadata in `'library-items'` and item notes in `'library-item-notes'`.
- If you need to store binary data (e.g. cover art), store a URL reference, not the raw data.

---

## No Backend

- The app has **no backend API, no server-side logic, and no authentication layer** beyond what Spark provides.
- Do not write `fetch()` calls to external APIs unless explicitly building an integration feature.
- Do not introduce server-side frameworks (Next.js API routes, Express, etc.).
- All business logic runs in the browser.

---

## Spark Hooks

The following hooks are available from `@github/spark/hooks`:

| Hook | Purpose |
|---|---|
| `useKV<T>(key, default)` | Persistent key-value storage |
| `useOctokit()` | GitHub API client (authenticated) |
| `useCurrentUser()` | Currently authenticated GitHub user |
| `useColorScheme()` | Current colour scheme (`'light'` \| `'dark'` \| `'auto'`) |

- Only use `useOctokit()` and `useCurrentUser()` when building GitHub-specific features.
- Do not polyfill or mock these hooks in tests — test the logic that consumes their output instead.

---

## Build & Tooling

- The project uses `@github/spark` Vite plugin for building.
- Do not eject or modify the Vite config unless absolutely necessary.
- The `@/` import alias is configured via `vite.config.ts` and `tsconfig.json` — use it for all internal imports.
- Run `npm run dev` to start the development server; `npm run build` to build for production.

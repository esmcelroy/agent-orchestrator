# TypeScript Coding Standards

Standards for all TypeScript code in `home-library-tracker`. Follows the project's `tsconfig` which has `strict: true` enabled.

---

## Strict Mode

- **`strict: true` is non-negotiable.** All code must compile cleanly under strict mode with no errors.
- **Never use `any`** unless you are writing a type-narrowing utility and even then, prefer `unknown`.
- **Never use `// @ts-ignore`** without a comment explaining exactly why it is necessary and what the correct fix would be.
- **Never use `// @ts-nocheck`** — not even temporarily.
- If you encounter a third-party library with missing types, add a declaration file (`*.d.ts`) or use `// @ts-expect-error` with a comment.

---

## Types vs Interfaces

- Use **`interface`** for **object shapes** — domain models, component props, API response shapes.
- Use **`type`** for **unions, intersections, aliases, and mapped types**.
- Do not mix the two arbitrarily. Pick the right tool:

```ts
// ✅ Correct
interface LibraryItem {
  id: string;
  title: string;
  mediaType: MediaType;
}

type MediaType = 'book' | 'vinyl' | 'cd' | 'dvd' | 'game';

// ❌ Avoid
type LibraryItem = { id: string; title: string; }; // use interface
interface MediaType = 'book' | ...; // not valid — use type
```

---

## Discriminated Unions for State Machines

Use discriminated unions when modelling states that have mutually exclusive shapes:

```ts
// ✅ Correct — discriminated union
type ScanState =
  | { status: 'idle' }
  | { status: 'scanning' }
  | { status: 'success'; barcode: string }
  | { status: 'error'; message: string };

// ❌ Avoid — flat nullable fields
interface ScanState {
  isScanning: boolean;
  barcode?: string;
  error?: string;
}
```

This makes exhaustive `switch` statements possible and eliminates impossible states.

---

## Return Types

- **Always annotate return types on exported functions** and exported React components.
- Return types on private/internal helpers are optional but encouraged for complex functions.

```ts
// ✅ Correct
export function formatMediaType(type: MediaType): string { ... }
export const LibraryItemCard = ({ item }: LibraryItemCardProps): JSX.Element => { ... };

// ❌ Avoid
export function formatMediaType(type: MediaType) { ... } // missing return type
```

---

## No Barrel Files

- **Do not create `index.ts` barrel files** unless there is a compelling reason (e.g. a published package boundary).
- Import directly from the source file:

```ts
// ✅ Correct
import { LibraryItem } from "@/lib/types";

// ❌ Avoid
import { LibraryItem } from "@/lib"; // relies on barrel
```

---

## Naming Conventions

| Category | Convention | Example |
|---|---|---|
| Types / Interfaces | PascalCase | `LibraryItem`, `MediaType` |
| React components | PascalCase | `LibraryItemCard`, `ItemForm` |
| Functions & methods | camelCase | `formatMediaType`, `handleSubmit` |
| Variables | camelCase | `filteredItems`, `selectedType` |
| Constants (module-level, immutable) | SCREAMING_SNAKE_CASE | `DEFAULT_CONDITION`, `MAX_TITLE_LENGTH` |
| Custom hooks | camelCase, `use` prefix | `useFilteredItems`, `useBarcodeScanner` |
| Enum values | PascalCase (prefer `type` unions instead) | — |

---

## Nullability

- Prefer `T | null` over `T | undefined` for values that are intentionally absent.
- Use `T | undefined` for optional function parameters and optional object properties.
- Never return `null` from a React component — return an empty fragment (`<>`) instead.

---

## Generics

- Name single-purpose generic parameters `T` or descriptively (`TItem`, `TKey`).
- Do not over-generalise — write a concrete type first and only extract a generic when the same shape is needed in two or more places.

---

## Imports

- Use absolute imports via the `@/` alias (configured in `tsconfig.json` / `vite.config.ts`).
- Order imports: external packages → internal absolute (`@/`) → relative (`./`).
- Do not mix default and named imports from the same module in confusing ways.

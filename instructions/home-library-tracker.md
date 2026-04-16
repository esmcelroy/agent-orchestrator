# Home Library Tracker — Project-Specific Copilot Instructions

These instructions apply whenever you are working on the `home-library-tracker` repository (*Library Keeper*). They supplement the global instructions in `instructions/global.md`.

---

## State & Persistence

- **Always use the `useKV` hook** for any state that needs to survive a page refresh or be shared across sessions. Do not use `useState` for persistent data.
- Import `useKV` from `@github/spark/hooks`.
- KV keys must follow the namespacing convention defined in `standards/spark.md`.
- Never use `localStorage`, `sessionStorage`, `IndexedDB`, or cookies directly.

---

## Styling

- **Use Tailwind utility classes** for all styling. Do not write custom CSS unless there is no Tailwind equivalent.
- When you need a value that Tailwind does not cover (e.g. a very specific pixel measurement), use a Tailwind arbitrary value (`[value]`) before reaching for a `<style>` block.
- Do not modify `tailwind.config.*` unless you are explicitly asked to configure a design token.
- Keep class lists readable — if a class list exceeds ~10 items, extract the element into a named component.

---

## UI Components

- **Use shadcn/ui components** from `@/components/ui/` before creating new UI primitives. The following are already available: `Button`, `Input`, `Label`, `Select`, `Dialog`, `Badge`, `Tabs`, `Card`, `Tooltip`, `Sheet`, `Popover`, and others.
- Do not duplicate functionality that shadcn/ui already provides.
- When adding a new shadcn/ui component that is not yet installed, use `npx shadcn@latest add <component>` — do not copy-paste component code manually.

---

## Icons

- **Use Phosphor Icons exclusively.** Do not use Lucide, Heroicons, or any other icon library.
- Import icons from `@phosphor-icons/react`.
- Use the `weight` prop to control icon style (`"regular"` | `"bold"` | `"fill"` | `"duotone"` | `"light"` | `"thin"`).
- Example: `import { BookOpen, MusicNote } from "@phosphor-icons/react";`

---

## Types & Data Shapes

- **Follow the existing `LibraryItem` type shape.** Never rename, remove, or change the type of an existing field.
- Add new fields to `LibraryItem` and related types in `src/lib/types.ts`. Do not define domain types inline in components.
- New types should use `interface` for object shapes and `type` for unions/aliases (see `standards/typescript.md`).
- `MediaType` values are: `'book' | 'vinyl' | 'cd' | 'dvd' | 'game'`. Do not add new values without updating all exhaustive switches.

---

## File & Component Organisation

- Components live in `src/components/`. Shared UI primitives live in `src/components/ui/`.
- Custom hooks live in `src/hooks/` and must be prefixed with `use`.
- Utility functions live in `src/lib/`.
- Do not create deeply nested component folders — keep the structure flat.

---

## Animations

- Use **`AnimatePresence`** from `framer-motion` for **enter/exit animations on lists and conditional renders**.
- Use **`motion.div`** (or the appropriate `motion.*` element) for transition animations.
- Wrap list renders in `AnimatePresence` with a unique `key` on each child.
- Keep animation durations short (0.15–0.3 s) to avoid feeling sluggish on mobile.

---

## Notifications

- Use **`sonner`** for all toast notifications. Import `toast` from `"sonner"`.
- Use `toast.success()` for confirmations, `toast.error()` for failures, `toast.info()` for neutral messages.
- Keep toast messages short (< 60 characters).
- Always show a toast after a user-initiated action that mutates data (add, edit, delete, import, export).

---

## Mobile Responsiveness

- Every new UI feature must work on a 375 px wide viewport. Test layouts at mobile size before considering a feature complete.
- Use responsive Tailwind prefixes (`sm:`, `md:`, `lg:`) rather than writing separate mobile and desktop components.
- Touch targets must be at least 44 × 44 px.

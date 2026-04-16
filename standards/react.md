# React Coding Standards

Standards for all React code in `home-library-tracker`. The project uses React 18 with TypeScript strict mode.

---

## Component Style

- **Functional components only.** Do not write class components.
- Export components as named exports, not default exports (makes refactoring and search easier).
- Every component file should export exactly one primary component. Co-located sub-components are fine if they are not used elsewhere.

```tsx
// ✅ Correct
export const LibraryItemCard = ({ item }: LibraryItemCardProps): JSX.Element => { ... };

// ❌ Avoid
export default function LibraryItemCard(...) { ... }
class LibraryItemCard extends React.Component { ... }
```

---

## Props

- Props interfaces must be named **`[ComponentName]Props`**.
- Always define a props interface — do not inline prop types or use `React.FC<{...}>` generics.
- Mark props optional with `?` only when the component genuinely works without them (provide a sensible default).

```tsx
// ✅ Correct
interface LibraryItemCardProps {
  item: LibraryItem;
  onEdit: (id: string) => void;
  onDelete: (id: string) => void;
}

export const LibraryItemCard = ({ item, onEdit, onDelete }: LibraryItemCardProps): JSX.Element => { ... };
```

---

## Custom Hooks

- All custom hooks live in `src/hooks/` and **must be prefixed with `use`**.
- A hook should do one thing. If a hook grows beyond ~50 lines, consider whether it should be split.
- Hooks must not be called conditionally inside components.
- Always return a stable, typed object or tuple — document the shape with an interface or type alias.

```ts
// ✅ Correct
// src/hooks/useFilteredItems.ts
export function useFilteredItems(items: LibraryItem[], filters: Filters): LibraryItem[] { ... }
```

---

## Performance

- Use **`useMemo`** for expensive derivations (e.g. filtering/sorting large arrays) that depend on props or state.
- Use **`useCallback`** for event handler functions passed as props to child components, to avoid unnecessary re-renders.
- Do not wrap every function in `useCallback` — only use it when the callback is passed as a prop or used as a `useEffect` dependency.

```tsx
// ✅ Correct
const filteredItems = useMemo(
  () => items.filter(item => item.mediaType === selectedType),
  [items, selectedType]
);

const handleDelete = useCallback((id: string) => {
  setItems(prev => prev.filter(item => item.id !== id));
}, [setItems]);
```

---

## Component Size

- **Keep components under ~200 lines.** If a component exceeds this, split it.
- Extract a sub-component when:
  - A block of JSX would be reused elsewhere, OR
  - A section has its own local state that is unrelated to the parent, OR
  - The component file exceeds ~200 lines
- Do not extract micro-components (< 10 lines of JSX) that exist only to reduce line count.

---

## Prop Drilling

- **Avoid prop drilling beyond 2 levels.** If a prop is needed 3+ levels down:
  - Lift state to the lowest common ancestor and pass down a callback, OR
  - Create a React context for widely-shared state (e.g. current filters, user preferences)
- Do not use a global state library (Redux, Zustand, etc.) — the app is simple enough for context + `useKV`.

---

## Animations

- Use **`AnimatePresence`** from `framer-motion` for list enter/exit animations.
- Wrap conditional renders and list renders with `AnimatePresence`. Always provide a unique `key` on each animated child.
- Use **`motion.div`** (or other `motion.*` elements) with `initial`, `animate`, and `exit` props.
- Keep animations tasteful — duration 0.15–0.25 s, subtle scale or opacity transitions.

```tsx
// ✅ Correct
<AnimatePresence>
  {items.map(item => (
    <motion.div
      key={item.id}
      initial={{ opacity: 0, y: 8 }}
      animate={{ opacity: 1, y: 0 }}
      exit={{ opacity: 0, y: -8 }}
      transition={{ duration: 0.2 }}
    >
      <LibraryItemCard item={item} />
    </motion.div>
  ))}
</AnimatePresence>
```

---

## Conditional Rendering

- Prefer **early returns** over deeply nested ternaries.
- Use `&&` for simple conditional renders where the falsy case renders nothing.
- Use ternary (`? :`) only when both branches render something meaningful.
- Never render `null` from a component — return `<></>` for empty output.

---

## Error Boundaries & Empty States

- Every list or data-driven view must handle the **empty state** with a meaningful message and a call to action.
- Loading states should use a skeleton or spinner, not a blank screen.
- If a feature can error (e.g. barcode scanning), wrap it in a try/catch and show a user-visible error via `toast.error()`.

---

## File Naming

- Component files: `PascalCase.tsx` matching the component name (e.g. `LibraryItemCard.tsx`).
- Hook files: `camelCase.ts` matching the hook name (e.g. `useFilteredItems.ts`).
- Utility files: `camelCase.ts` (e.g. `formatters.ts`, `validators.ts`).
- Test files: `[ComponentName].test.tsx` co-located with the component.

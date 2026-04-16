# Skill: Add a New Feature to Library Keeper

Use this template when you want to add a new capability to the `home-library-tracker` app. Follow the steps in order — do not skip ahead to implementation.

---

## Prompt Template

> I want to add the following feature to Library Keeper:
>
> **[Describe the feature in one or two sentences. What does the user see? What can they do?]**
>
> Please follow the steps in `skills/add-feature.md`.

---

## Steps

### Step 1 — Read the Relevant Files

Before writing any code, read these files:

1. **`src/lib/types.ts`** — understand all current domain types. Note whether the feature requires new fields or new types.
2. **`src/App.tsx`** — understand the current KV keys, top-level state, and how components are composed.
3. **The component(s) most likely to be affected** by the feature (e.g. `ItemForm.tsx` for form changes, `LibraryItemCard.tsx` for card changes).
4. **`PRD.md`** — check whether this feature is already planned and if there are any notes about the intended design.

### Step 2 — Identify Affected Components

List every file that will need to change:
- New types or type extensions → `src/lib/types.ts`
- New KV keys or state → `src/App.tsx` (or a new custom hook in `src/hooks/`)
- New or modified UI → relevant component files
- New constants → `src/lib/constants.ts`

If the feature requires a **new component**, confirm the name and location before creating it.

### Step 3 — Plan the Changes

Write a brief, ordered list of changes:
1. Add/extend types in `src/lib/types.ts`
2. Add KV state / update existing state
3. Implement the UI (component changes)
4. Wire up state to UI
5. Add toast notifications for data mutations

Share the plan before implementing if more than 3 files are affected.

### Step 4 — Implement

- **Start with types.** Always update `src/lib/types.ts` first.
- **Then data.** Update `useKV` calls and any derived state.
- **Then UI.** Build or modify components following `standards/react.md`.
- Follow all conventions in `instructions/home-library-tracker.md`:
  - Use `useKV` for persistent state
  - Use Tailwind classes for styling
  - Use shadcn/ui components from `@/components/ui/`
  - Use Phosphor Icons
  - Use `AnimatePresence` for list transitions
  - Use `sonner` for toasts

### Step 5 — Update Types if Needed

If you added fields to `LibraryItem` or related types:
- Check all existing code that constructs or consumes a `LibraryItem` (e.g. `ItemForm.tsx`, filter logic in `App.tsx`) and update it.
- Ensure TypeScript compiles cleanly with `tsc --noEmit`.

---

## Feature Completion Checklist

Before considering the feature done, verify every item:

- [ ] **Responsive layout** — feature works on a 375 px wide viewport; no horizontal overflow
- [ ] **Toast feedback** — success and error toasts fire for all user-initiated mutations
- [ ] **Empty state** — if the feature includes a list or data view, there is a meaningful empty state message
- [ ] **Type safety** — `tsc --noEmit` passes with no new errors
- [ ] **No regressions** — existing features (add/edit/delete item, filters, wishlist, barcode scanner) still work
- [ ] **Accessibility** — interactive elements have labels; focus management is correct for modals/dialogs
- [ ] **Mobile touch targets** — all buttons/tappables are at least 44 × 44 px
- [ ] **KV key documented** — any new KV key is added to the `KV_KEYS` constant in `src/lib/constants.ts`

---

## Common Patterns

### Adding a new field to an item

1. Add the field (optional) to `LibraryItem` in `src/lib/types.ts`.
2. Add the field to `ItemForm.tsx` (input + label + validation).
3. Display the field in `LibraryItemCard.tsx` if it should be visible in the card.
4. Ensure the field is preserved in edit flows (`handleEdit` in `App.tsx`).

### Adding a new filter

1. Add the filter key to the filters `type`/`interface` in `src/lib/types.ts`.
2. Add UI for the filter in the filter bar (likely in `App.tsx` or a `FilterBar` component).
3. Update the `useMemo` that derives `filteredItems` to apply the new filter.
4. Persist the filter state in KV if it should survive a page refresh.

### Adding a new tab

1. Add the tab to the `Tabs` component in `App.tsx`.
2. Create the tab content as a new component in `src/components/`.
3. Ensure the tab is keyboard-accessible and visible on mobile.

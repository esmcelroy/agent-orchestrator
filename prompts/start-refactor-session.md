# Start Refactor Session — Home Library Tracker

Paste this prompt at the start of any conversation where you are refactoring existing code in the app.

---

## Session prompt

> I want to refactor a component or module in the **home-library-tracker** (Library Keeper) app.
>
> **Refactor target:** [describe the component, hook, or file to refactor]
>
> **Before writing any code**, please:
>
> 1. Read the **full** target component or module — do not start until you have read it completely.
> 2. Note all props, callbacks, and state it manages.
> 3. Note all ARIA attributes and accessibility behaviour present.
>
> **Refactoring rules:**
>
> - Preserve all existing props and their types exactly — no renames, no removals.
> - Preserve all existing behaviour — no functional changes as part of a refactor.
> - Preserve all ARIA attributes (`aria-label`, `role`, `aria-expanded`, etc.).
> - **Prefer extracting custom hooks** over restructuring JSX. Moving logic into a hook is the safest refactor pattern for this codebase.
> - Do not change the `LibraryItem` type shape as part of a refactor.
>
> **Top extraction candidates from `App.tsx`** (highest-value refactors):
>
> | Proposed hook | State / logic to extract |
> |---|---|
> | `useLibraryFilters` | `selectedMediaTypes`, `selectedLocations`, `selectedConditions`, `searchQuery`, and the `filteredItems` `useMemo` |
> | `useLibraryStats` | Logic that derives stats (total count, per-type counts, wishlist count) from the `items` array |
> | `useDialogState` | `dialogMode`, `editingItem`, `scannedBarcode`, `deletingItem`, and all open/close/reset handlers |
>
> **Completion checklist:**
>
> - [ ] All existing props and callbacks preserved with identical signatures
> - [ ] All ARIA/accessibility attributes preserved
> - [ ] TypeScript compiles cleanly (`tsc --noEmit`) with no new errors or warnings
> - [ ] No regressions: filtering, dialog flows, and toast notifications all work as before
> - [ ] No changes to `LibraryItem` type shape

---

## After the session

Update `memory/changelog.md` with a brief note on what was refactored and which hooks were extracted. If the refactor resolves any item in `memory/known-issues.md` (e.g. "App.tsx too large"), update that file accordingly.

# Start Feature Session — Home Library Tracker

Paste this prompt at the start of any conversation where you are adding a new feature to the app.

---

## Session prompt

> I want to add a new feature to the **home-library-tracker** (Library Keeper) app.
>
> **Before writing any code**, please:
>
> 1. Read `src/lib/types.ts` to understand the current `LibraryItem` shape and all related types.
> 2. Read `src/App.tsx` to understand the current state structure, `filteredItems` memo, dialog state, and render tree.
>
> **Then answer these scoping questions before touching any code:**
>
> - Does this feature require a new field on `LibraryItem`? If yes, what is the migration plan for existing KV data?
> - Does this feature require a new media type, location, or condition value?
> - Should this be a new component, or should an existing component be extended?
> - Does this feature require a new KV key, or does it operate on `'library-items'`?
>
> **Pre-implementation checklist — every feature must satisfy all of these:**
>
> - [ ] Responsive layout: works on mobile (375px) and desktop (1280px+)
> - [ ] Toast feedback on every mutation (add, edit, delete, or any async operation)
> - [ ] Empty state and loading state handled for any new list or async data
> - [ ] Full TypeScript type safety — no `any`, no non-null assertions without a comment
> - [ ] No regressions: `filteredItems` memo still works correctly, dialog state resets cleanly on close
>
> **Design conventions** — follow `memory/prd-snapshot.md` for:
> - Color tokens (burgundy primary, amber accent, paper background)
> - Typography (Crimson Pro for headings, Inter for body)
> - Icon-to-action mapping (Phosphor Icons only — see the mapping table)
> - Component interaction states (card hover, input focus ring, button variants)
>
> **Hard constraints** — review `instructions/constraints.md` before starting. Key rules:
> - No changes to `LibraryItem` shape without a KV migration plan
> - No inline styles — Tailwind only
> - No Lucide or Heroicons — Phosphor Icons only
> - Check `src/components/ui/` before building any new UI primitive

---

## After the session

Update `memory/changelog.md` with a new entry describing what was built, and update `memory/known-issues.md` if any new gaps were discovered or existing issues were resolved.

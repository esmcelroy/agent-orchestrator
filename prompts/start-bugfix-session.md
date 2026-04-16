# Start Bugfix Session — Home Library Tracker

Paste this prompt at the start of any conversation where you are debugging or fixing a bug in the app.

---

## Session prompt

> I need to fix a bug in the **home-library-tracker** (Library Keeper) app.
>
> **Bug description:** [describe the bug here]
>
> **Before touching anything**, please:
>
> 1. Read the component(s) most likely involved in the bug.
> 2. Read `src/App.tsx` to understand how state flows into and out of the component.
>
> **Debugging workflow — follow this order:**
>
> 1. **Reproduce** — identify the exact steps to trigger the bug and what the wrong behaviour is vs. the expected behaviour.
> 2. **Isolate** — determine whether the bug lives in a component, a hook, or the KV data layer. Check whether the wrong value is coming in via props, produced locally, or read from KV.
> 3. **Identify root cause** — read the code carefully before proposing a fix. State the root cause explicitly before writing any changes.
> 4. **Fix** — make the minimal change needed to correct the behaviour. Do not refactor unrelated code.
> 5. **Verify no regressions** — after fixing, confirm the following still work correctly:
>    - Filtering (`selectedMediaTypes`, `selectedLocations`, `selectedConditions`, `searchQuery`, `filteredItems` memo)
>    - Dialog flows (open → fill → submit/cancel → state resets)
>    - Toast notifications on mutations
>
> **Common gotchas specific to this codebase:**
>
> - **KV data shape mismatch** — items saved before a `LibraryItem` schema change will be missing new fields. Always check whether `item.newField ?? defaultValue` guards are needed when reading KV data.
> - **`filteredItems` memo dependency array incomplete** — if a new filter state variable is added but not included in the `useMemo` dependency array, filtering will silently stale. Always audit the dependency array when changing filter logic.
> - **Dialog state not reset on close** — `editingItem` and `scannedBarcode` can leak between dialog sessions if the close handler does not explicitly reset them to `null`/`undefined`. Check `onOpenChange` and the cancel handler for the affected dialog.

---

## After the session

Update `memory/changelog.md` with a brief note on what was fixed, and update `memory/known-issues.md` if the bug revealed a new pattern or systemic gap.

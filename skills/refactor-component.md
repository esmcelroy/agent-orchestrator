# Skill: Refactor a Component

Use this template when you need to clean up, restructure, or improve a component in `home-library-tracker` without changing its external behaviour.

---

## Prompt Template

> I want to refactor the `[ComponentName]` component in `home-library-tracker`.
>
> Goals: [describe what you want to improve — e.g. "split into smaller pieces", "extract custom hook", "improve readability", "fix TypeScript errors"]
>
> Please follow the steps in `skills/refactor-component.md`.

---

## Golden Rule

> **Read the component fully before changing anything.**

Do not make any edits until you have read the entire component file and understand:
- Every prop it accepts and what each does
- Every piece of local state and what triggers changes
- Every callback and event handler
- Every conditional render path (including edge cases like empty arrays, loading states, error states)
- Every child component it renders and what props it passes to them

---

## Steps

### Step 1 — Read Everything First

1. Read the **full component file** (`src/components/[ComponentName].tsx`).
2. Read every **child component** the component renders to understand their props contracts.
3. Read the **parent(s)** that render this component to understand what props are passed in.
4. Read the **custom hooks** used by the component (`src/hooks/`).
5. Read **`src/lib/types.ts`** for the types used in the component's props and state.

Do not proceed until you have a complete mental model of the component.

### Step 2 — Identify the Refactor Goals

Clearly state what will improve after the refactor. Examples:
- Component exceeds 200 lines → extract sub-components
- Repeated JSX blocks → extract a helper component
- Complex inline logic → extract to a custom hook
- Tangled state → separate concerns
- Missing type annotations → add explicit types
- Prop drilling → lift state or introduce context

### Step 3 — Plan Without Breaking Things

Before touching any code, map out:
- **What will stay the same** — all props, all visible behaviour, all rendered output
- **What will change** — internal structure, file organisation, naming
- **Risk areas** — filter logic, event handlers, animations, form validation

If the refactor involves moving state or extracting a hook, explicitly state the before/after of the state shape.

### Step 4 — Implement

Follow these rules during implementation:

#### Preserve all existing props and behaviour

- The component's `[ComponentName]Props` interface must remain **identical** (same fields, same types) unless the goal was explicitly to change the props API.
- Do not change the rendered HTML structure in ways that would break layout or styling.
- Do not remove any Tailwind classes without checking what they do visually.

#### Extract sub-components only when justified

Extract a piece of JSX into a new sub-component only if **at least one** of these is true:
- The sub-component would be **reused** in another component.
- The sub-component has **its own local state** that is unrelated to the parent.
- The parent component exceeds **150 lines** and the sub-component is a visually distinct section.

Do **not** extract micro-components just to reduce line count.

#### Extracting to a custom hook

If extracting state/logic to a custom hook:
- Place the hook in `src/hooks/[hookName].ts`.
- Name it with the `use` prefix.
- Return a **typed object** (not a positional tuple unless the hook only returns 2 values).
- Ensure all `useEffect` dependencies are correct — do not suppress lint warnings.

#### No regressions in filtering or state logic

- If the component contains filter logic or derived state (e.g. `useMemo` for filtered arrays), copy it exactly before refactoring and verify the output is identical after.
- Run `tsc --noEmit` after every meaningful change to catch type regressions early.

### Step 5 — Verify

- [ ] `tsc --noEmit` passes with no new errors
- [ ] All props are preserved with their original names and types
- [ ] All existing behaviour works (add, edit, delete, filter, animate, toast)
- [ ] Mobile layout is unaffected (test at 375 px width)
- [ ] No console warnings introduced (missing keys, missing deps, etc.)
- [ ] The extracted hook/component is clearly named and placed in the right directory

---

## When Not to Refactor

Stop and discuss with the user if:
- The refactor would require changing the `LibraryItem` data shape
- The refactor would change how KV state is structured
- The refactor would affect the barcode scanning feature in non-trivial ways
- You discover that the component has undocumented behaviour that could break

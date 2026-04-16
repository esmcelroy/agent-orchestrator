# Skill: Write Tests

Use this template when you want to add test coverage to a component, hook, or utility in `home-library-tracker`.

---

## Prompt Template

> I want to write tests for `[ComponentName / hookName / utilityName]` in `home-library-tracker`.
>
> Please follow the steps in `skills/write-tests.md`.

---

## Testing Philosophy for Spark Apps

GitHub Spark apps have no backend, so the testing focus is on:

1. **Unit tests for pure logic** — utilities, formatters, validators, filter functions, type guards. These are fast, deterministic, and have no dependencies.
2. **Component tests for UI** — test what the user sees and does, not implementation details. Use React Testing Library's `render`, `screen`, `userEvent` approach.

Do **not** test implementation details like internal state variables or private functions. Test behaviour that is observable from the outside.

---

## Setup

- Test runner: **Vitest** (compatible with Vite; use over Jest for this project)
- Component testing: **React Testing Library** (`@testing-library/react`)
- User interaction: **`@testing-library/user-event`**
- Mocking: Vitest's `vi.mock()` / `vi.fn()`

If Vitest is not yet set up, ask the user before installing it.

---

## File Naming & Location

- Test files must be named **`[ComponentName].test.tsx`** for components or **`[name].test.ts`** for hooks/utilities.
- Test files are **co-located** with the file they test:
  ```
  src/components/LibraryItemCard.tsx
  src/components/LibraryItemCard.test.tsx

  src/hooks/useFilteredItems.ts
  src/hooks/useFilteredItems.test.ts

  src/lib/formatters.ts
  src/lib/formatters.test.ts
  ```
- Do not put tests in a top-level `__tests__` folder.

---

## Steps

### Step 1 — Read the Code Under Test

1. Read the **full file** being tested.
2. Identify all exported functions, components, and hooks.
3. List the **inputs**, **outputs**, and **side effects** of each.
4. Note any edge cases, conditional branches, or error paths.

### Step 2 — Plan Test Coverage

For each unit under test, identify:
- **Happy path** — the normal, expected flow
- **Empty state** — what happens with no data (empty arrays, null values)
- **Error state** — what happens when something fails (bad input, async error)
- **Edge cases** — boundary values, unusual but valid inputs

### Step 3 — Write the Tests

#### Unit tests (pure functions and utilities)

```ts
// src/lib/formatters.test.ts
import { describe, it, expect } from 'vitest';
import { formatMediaType } from './formatters';

describe('formatMediaType', () => {
  it('formats known media types', () => {
    expect(formatMediaType('book')).toBe('Book');
    expect(formatMediaType('vinyl')).toBe('Vinyl');
  });

  it('handles all MediaType values', () => {
    const types: MediaType[] = ['book', 'vinyl', 'cd', 'dvd', 'game'];
    types.forEach(type => {
      expect(formatMediaType(type)).toBeTruthy();
    });
  });
});
```

#### Component tests

```tsx
// src/components/LibraryItemCard.test.tsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { describe, it, expect, vi } from 'vitest';
import { LibraryItemCard } from './LibraryItemCard';
import { mockLibraryItem } from '../lib/test-utils'; // shared test fixture

describe('LibraryItemCard', () => {
  it('renders item title and media type', () => {
    render(<LibraryItemCard item={mockLibraryItem} onEdit={vi.fn()} onDelete={vi.fn()} />);
    expect(screen.getByText(mockLibraryItem.title)).toBeInTheDocument();
  });

  it('calls onEdit when edit button is clicked', async () => {
    const onEdit = vi.fn();
    render(<LibraryItemCard item={mockLibraryItem} onEdit={onEdit} onDelete={vi.fn()} />);
    await userEvent.click(screen.getByRole('button', { name: /edit/i }));
    expect(onEdit).toHaveBeenCalledWith(mockLibraryItem.id);
  });

  it('calls onDelete when delete button is clicked', async () => {
    const onDelete = vi.fn();
    render(<LibraryItemCard item={mockLibraryItem} onEdit={vi.fn()} onDelete={onDelete} />);
    await userEvent.click(screen.getByRole('button', { name: /delete/i }));
    expect(onDelete).toHaveBeenCalledWith(mockLibraryItem.id);
  });
});
```

#### Hook tests (using `renderHook`)

```ts
// src/hooks/useFilteredItems.test.ts
import { renderHook } from '@testing-library/react';
import { describe, it, expect } from 'vitest';
import { useFilteredItems } from './useFilteredItems';
import { mockItems } from '../lib/test-utils';

describe('useFilteredItems', () => {
  it('returns all items when no filters are active', () => {
    const { result } = renderHook(() =>
      useFilteredItems(mockItems, { mediaType: null, location: null, condition: null })
    );
    expect(result.current).toHaveLength(mockItems.length);
  });

  it('filters by media type', () => {
    const { result } = renderHook(() =>
      useFilteredItems(mockItems, { mediaType: 'book', location: null, condition: null })
    );
    expect(result.current.every(item => item.mediaType === 'book')).toBe(true);
  });

  it('returns empty array when no items match', () => {
    const { result } = renderHook(() =>
      useFilteredItems([], { mediaType: 'vinyl', location: null, condition: null })
    );
    expect(result.current).toHaveLength(0);
  });
});
```

---

## Test Coverage Checklist

For each file you test, ensure you cover:

- [ ] **Happy path** — normal usage with realistic data
- [ ] **Empty state** — component renders correctly with no items / null values
- [ ] **Error state** — error messages display; error callbacks fire
- [ ] **Edge cases on filter logic** — multiple filters active simultaneously; filters with no matches; filters cleared
- [ ] **User interactions** — click, keyboard, form submit tested with `userEvent`
- [ ] **Prop callbacks** — `onEdit`, `onDelete`, `onChange` etc. called with correct arguments
- [ ] **Accessibility** — critical elements accessible by role (`getByRole` preferred over `getByTestId`)

---

## Mocking `useKV`

`useKV` from `@github/spark/hooks` must be mocked in component tests:

```ts
vi.mock('@github/spark/hooks', () => ({
  useKV: vi.fn((key, defaultValue) => [defaultValue, vi.fn()]),
}));
```

Place this at the top of the test file, before the `describe` block.

---

## Shared Test Fixtures

Create shared mock data in `src/lib/test-utils.ts` to keep tests DRY:

```ts
// src/lib/test-utils.ts
import type { LibraryItem } from './types';

export const mockLibraryItem: LibraryItem = {
  id: 'test-id-1',
  title: 'Test Book',
  mediaType: 'book',
  author: 'Test Author',
  location: 'shelf',
  condition: 'good',
  isWishlisted: false,
  createdAt: new Date().toISOString(),
};

export const mockItems: LibraryItem[] = [
  mockLibraryItem,
  { ...mockLibraryItem, id: 'test-id-2', title: 'Test Vinyl', mediaType: 'vinyl' },
  { ...mockLibraryItem, id: 'test-id-3', title: 'Test Game', mediaType: 'game' },
];
```

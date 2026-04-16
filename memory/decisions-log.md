# Decisions Log

ADR-style log of architectural and technology decisions for `home-library-tracker`. Add a new entry whenever a significant decision is made. Keep entries in reverse chronological order (newest first).

---

## Template

```
### ADR-NNN: [Short title]
- **Date**: YYYY-MM-DD
- **Status**: Accepted | Superseded by ADR-NNN | Deprecated
- **Decision**: [What was decided]
- **Context**: [Why a decision was needed]
- **Rationale**: [Why this option was chosen over alternatives]
- **Consequences**: [What changes, what gets easier, what gets harder]
```

---

## Entries

---

### ADR-003: Use Phosphor Icons Over Lucide React

- **Date**: 2025-01-01
- **Status**: Accepted
- **Decision**: Use `@phosphor-icons/react` as the sole icon library. Do not install or use Lucide, Heroicons, or any other icon set.
- **Context**: shadcn/ui defaults to Lucide icons, and many React projects use Lucide as a de-facto standard. A choice needed to be made before building out the icon-heavy media type indicators and navigation.
- **Rationale**:
  - Phosphor Icons was already chosen early in the project and used in the initial `MediaIcon` component.
  - Phosphor has a richer set of media-specific icons (vinyl records, game controllers, film reels) that Lucide lacks.
  - Phosphor supports multiple weights (`regular`, `bold`, `fill`, `duotone`, `light`, `thin`) from a single import, reducing the need for workarounds.
  - Mixing two icon libraries would create visual inconsistency and increase bundle size.
- **Consequences**:
  - Any new component that needs an icon must use Phosphor — check `phosphoricons.com` for available icons.
  - shadcn/ui components that render Lucide icons internally (e.g. sort indicators) may look slightly different from custom icons. Accept this or replace the internal icons via the `className` prop.
  - Bundle size is acceptable — Phosphor is tree-shaken by Vite so only imported icons are included.

---

### ADR-002: Use shadcn/ui Over Writing Custom Components

- **Date**: 2025-01-01
- **Status**: Accepted
- **Decision**: Use shadcn/ui (via `npx shadcn@latest add`) for all UI primitives. Do not build custom dialog, select, tooltip, popover, or tab components from scratch.
- **Context**: The app needed a consistent, accessible component library. Options considered: build from scratch, use a headless library directly (Radix UI), or use a pre-styled system (shadcn/ui, Chakra UI, MUI).
- **Rationale**:
  - shadcn/ui provides Radix UI primitives pre-wired with accessible semantics (focus management, ARIA roles, keyboard navigation) out of the box.
  - Components are copied into the repo (`src/components/ui/`) so they can be customised without fighting a third-party API.
  - Tailwind CSS styling means components integrate naturally with the rest of the app's styling system.
  - Chakra UI and MUI bring their own styling systems (Emotion, CSS-in-JS) that conflict with Tailwind.
  - Building from scratch would require significant accessibility work for free.
- **Consequences**:
  - Adding a new primitive means running `npx shadcn@latest add <component>` — do not copy-paste from the shadcn/ui docs manually.
  - The files in `src/components/ui/` are owned by the project — they can be modified but should be treated as a baseline.
  - Upgrading shadcn/ui components is manual (re-run the add command or diff manually).

---

### ADR-001: Use GitHub Spark KV Instead of a Backend API

- **Date**: 2025-01-01
- **Status**: Accepted
- **Decision**: All persistent data is stored in GitHub Spark's `useKV` hook. There is no backend API, no database, and no server-side logic.
- **Context**: Library Keeper is a personal tool for a single user. A decision was needed about how to persist the library collection (items, wishlist, preferences) across sessions.
- **Rationale**:
  - **Simplicity**: `useKV` provides get/set persistence with a React hook interface. No API design, no auth, no deployment of a server.
  - **GitHub Spark constraint**: Spark apps are designed to be fully client-side. Adding a backend would require hosting infrastructure outside of Spark.
  - **Single-user scope**: This is a personal tool. The trade-offs of a shared backend (conflict resolution, data sync, auth) are not worth it for one user.
  - **No data loss risk**: Spark KV is managed by GitHub — no risk of losing data due to a misconfigured server or expired hosting subscription.
- **Consequences**:
  - The app cannot support multiple users sharing a library without significant architectural changes.
  - Data lives in Spark's infrastructure — export/backup functionality (a planned feature) is important to give the user data portability.
  - Large collections may approach KV size limits; the data model should be kept lean (no base64 images, no redundant derived fields).
  - All business logic runs in the browser — this is a feature (offline capable, fast) not a limitation.

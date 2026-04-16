# agent-orchestrator

A portable, reusable AI agent toolkit for the [home-library-tracker](https://github.com/esmcelroy/home-library-tracker) project. This repository holds all the instructions, coding standards, agent definitions, skills, and memory that power consistent, high-quality AI-assisted development on *Library Keeper*.

---

## What This Repo Is

`agent-orchestrator` acts as the **brain** behind AI coding agents working on `home-library-tracker`. Rather than scattering prompts, rules, and context across different chats, everything lives here in version-controlled markdown files. When you open a Copilot Space with both repos attached, every conversation automatically has access to:

- **Behaviour rules** — how agents should communicate and approach tasks
- **Coding standards** — TypeScript, React, and GitHub Spark conventions
- **Agent definitions** — persona, goals, and constraints for the Library Keeper Agent
- **Skills** — reusable prompt templates for common tasks (add feature, refactor, write tests)
- **Memory** — persistent context about the project's current state and past decisions

---

## Folder Structure

```
agent-orchestrator/
├── README.md                          ← This file
│
├── instructions/
│   ├── global.md                      ← Universal Copilot behaviour rules (tone, approach)
│   └── home-library-tracker.md        ← Project-specific Copilot instructions
│
├── standards/
│   ├── typescript.md                  ← TypeScript conventions (strict mode, naming, types)
│   ├── react.md                       ← Component patterns, hooks rules, animations
│   └── spark.md                       ← GitHub Spark conventions (useKV, KV keys, no backend)
│
├── agents/
│   └── home-library-tracker-agent.md  ← Library Keeper Agent persona, goals, constraints
│
├── skills/
│   ├── add-feature.md                 ← Reusable prompt template: add a new feature
│   ├── refactor-component.md          ← Reusable prompt template: refactor a component
│   └── write-tests.md                 ← Reusable prompt template: write tests
│
└── memory/
    ├── project-context.md             ← Current state, features, known gaps, key files
    └── decisions-log.md               ← ADR-style log of past architectural decisions
```

---

## How to Use This With a Copilot Space

1. **Create a Copilot Space** (or open an existing one).
2. **Attach both repositories**:
   - `esmcelroy/agent-orchestrator` — the toolkit (this repo)
   - `esmcelroy/home-library-tracker` — the app you're working on
3. **Start a conversation.** The agent will have full context from both repos.
4. **Reference specific files** by path when you want an agent to focus on a particular standard, skill, or memory entry. For example:
   > "Using the workflow in `skills/add-feature.md`, add a sorting feature to Library Keeper."

### Recommended Conversation Starters

| Goal | Prompt |
|------|--------|
| Add a new feature | "Follow `skills/add-feature.md` and add [feature] to Library Keeper." |
| Refactor a component | "Follow `skills/refactor-component.md` and refactor `LibraryItemCard`." |
| Write tests | "Follow `skills/write-tests.md` and write tests for `ItemForm`." |
| Check standards compliance | "Review my changes against `standards/typescript.md` and `standards/react.md`." |
| Orient a new agent | "Read `agents/home-library-tracker-agent.md` and `memory/project-context.md`, then ask me what to work on." |

---

## How `.github/copilot-instructions.md` Ties In

The `home-library-tracker` repo contains `.github/copilot-instructions.md`. This file provides a concise summary of the coding standards from this toolkit so that **any** Copilot-enabled context — not just a Space with both repos attached — picks up the core rules automatically.

Think of it as:
- `agent-orchestrator` = the full source of truth (detailed, versioned)
- `.github/copilot-instructions.md` = the lightweight auto-loaded summary (always active)

When the two diverge, update `.github/copilot-instructions.md` in `home-library-tracker` to reflect changes made here.

---

## Updating the Toolkit

| What changed | What to update |
|---|---|
| New tech decision | `memory/decisions-log.md` + relevant `standards/` file |
| New feature added | `memory/project-context.md` (current feature set) |
| New reusable workflow | New file under `skills/` |
| Agent persona changed | `agents/home-library-tracker-agent.md` |
| Universal behaviour rule | `instructions/global.md` |
| Project-specific rule | `instructions/home-library-tracker.md` |

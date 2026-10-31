# Planning Skills

Two OpenCode skills for turning a request or a code review into an executable, test-first plan.

## Skills

### `implementation-planning`

Use when the user asks to build, add, change, or fix behavior and needs a decision-complete plan grounded in the current codebase.

It:

1. Checks `docs/codemap.md`; if missing or stale, dispatches a `repo-gist` subagent to refresh it.
2. Asks 3–5 decision questions in chat, each with 2–4 options and a recommended default.
3. Waits for the user’s selections before planning.
4. Builds an Impact Map from the codemap: component, file, why affected, required change, test.
5. Writes a `# Feature:` / `# Fix:` / `# Problem Statement:` plan.
6. Requires every task to name Skills, Files, Interfaces, RED/GREEN steps, a reviewer gate, and a commit.
7. Stops before implementation.

### `review-fix-planning`

Use when a code review returns findings (Greptile, human, or bot) and you need a test-first implementation plan with explicit reviewer gates.

It:

1. Builds a finding ledger — no finding disappears silently.
2. Re-grades findings by user impact, not label.
3. Maps each finding to an independently reviewable task.
4. Requires a failing regression test before every fix.
5. Adds plan-level reviewers: Product/UX, Technical, Plan/Test.
6. Adds a per-task reviewer gate and one final whole-branch reviewer.
7. Stops before implementation.

## Install

Copy the skill folders into your OpenCode skills directory:

```bash
cp -r implementation-planning review-fix-planning ~/.config/opencode/skills/
```

Restart OpenCode or reload skills. Both skills should appear in the available-skills list.

## Layout

```text
planning-skills/
├── implementation-planning/
│   ├── SKILL.md
│   └── VALIDATION.md
└── review-fix-planning/
    ├── SKILL.md
    └── VALIDATION.md
```

`VALIDATION.md` records the baseline failure without the skill and the validated behavior with it.

## Design

- Descriptions start with “Use when…” and name only the trigger.
- Skills are built with TDD: baseline failure → minimal skill → validation → refactor.
- Customer/project-specific rules stay out; these skills reference the project’s own `AGENTS.md`, `docs/codemap.md`, and verification commands.
- No implementation code is written while planning.

## License

MIT — see [LICENSE](LICENSE).

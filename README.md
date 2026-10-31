# Planning Skills

Three OpenCode skills for mapping a codebase, turning a request into an executable plan, and turning a review into a test-first fix plan.

## Skills

### `repo-gist`

Use when asked to gist, map, onboard to, or summarize a repository.

It:

1. Reads the README, manifests, tree, CI, and environment example.
2. Finds entry points and traces 2–3 core flows through the actual code.
3. Writes `docs/codemap.md`, `docs/routing.md`, and `docs/observation.md` (or `docs/gist/` if `docs/` already exists).
4. Self-checks every cited path and marks anything unverified.
5. Prints a 5-line summary and the files written.

`implementation-planning` uses this skill as its codemap refresh step when `docs/codemap.md` is missing or stale.

### `implementation-planning`

Use when the user asks to build, add, change, or fix behavior and needs a decision-complete plan grounded in the current codebase.

It:

1. Checks `docs/codemap.md`; if missing or stale, uses the `repo-gist` skill through a subagent to refresh it.
2. Uses the OpenCode `question` palette for 3–5 decision questions, each with 2–4 options and a recommended default, plus a final free-form notes question.
3. Waits for the user’s selections and free-form notes before planning.
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
cp -r repo-gist implementation-planning review-fix-planning ~/.config/opencode/skills/
```

Restart OpenCode or reload skills. All three should appear in the available-skills list.

## Layout

```text
planning-skills/
├── repo-gist/
│   └── SKILL.md
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
- Decision questions use the harness `question` palette when available; the fallback is a numbered chat list.

## License

MIT — see [LICENSE](LICENSE).

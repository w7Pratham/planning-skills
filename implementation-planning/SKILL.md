---
name: implementation-planning
description: Use when the user asks to build, add, change, or fix behavior and needs a decision-complete plan grounded in the current codebase.
---

# Implementation Planning

## Overview

Before planning a feature or fix, ask the user for the decisions only they can make, check the codebase map, and map every affected component to a test. Then write a plan where each task names its skills, interfaces, failing test, implementation step, reviewer gate, and commit.

**Core principle:** The plan is the set of decisions the implementer cannot make alone. Ask for those decisions; do not invent them.

## When to use

Use when:
- the user asks for a new feature;
- behavior changes with unclear scope;
- a bug fix touches multiple components;
- review findings need a plan.

Do not use when:
- the user supplied a complete plan already;
- the change is one obvious line and needs no decisions;
- the user asks a read-only question;
- it is time to execute (use `executing-plans` or `subagent-driven-development`).

## Workflow

### 1. Check the codemap

Look for `docs/codemap.md`.

- **Missing or stale:** dispatch a `repo-gist` subagent to create or refresh the codemap. Do not plan from memory.
- **Present:** read the module map, data model, flows, and test coverage.

If no subagent tool exists, inspect the repository and state that the codemap was reconstructed manually.

### 2. Ask decision questions with the option palette

Use the harness **`question` tool** — the multiple-choice option palette. Call it once with 3–5 decision questions plus one final notes question. Every question has 2–4 options; put the recommended option first and label it `(Recommended)`. The palette automatically adds a free-form “Type your own answer” option.

```python
question(questions=[
    {
        "header": "Scope",
        "question": "How large should this change be?",
        "options": [
            {"label": "Smallest safe change (Recommended)", "description": "Fix the immediate problem with the least surface area."},
            {"label": "Full feature", "description": "Implement the complete behavior in one plan."},
        ],
    },
    {
        "header": "Data",
        "question": "Should this change the stored data?",
        "options": [
            {"label": "No schema change (Recommended)", "description": "Use existing fields and tables."},
            {"label": "Additive migration", "description": "Add columns/tables without breaking existing data."},
        ],
    },
    {
        "header": "Notes",
        "question": "Anything else I should know before planning?",
        "options": [
            {"label": "No extra notes (Recommended)", "description": "Proceed with the choices above."},
            {"label": "I have notes", "description": "Add constraints or context in the free-form answer."},
        ],
    },
])
```

Wait for the user’s selections before writing the plan.

If the `question` tool is unavailable, ask the same questions in chat as a numbered list and wait for a reply such as `1A 2B 3C`.

Rules:
- No plan before answers.
- If the user says “you decide”, choose the recommended option and record a ruling.
- Ask a follow-up only when an answer creates a blocking ambiguity.
- Do not ask more than five decision questions plus the final notes question.
- Use the user’s free-form notes as plan constraints; do not discard them.

### 3. Build the Impact Map

Walk the codemap and list every affected area:

- entry points and handlers,
- agent/tool code,
- storage and data model,
- background loops,
- customer copy and UX,
- tests,
- docs.

```markdown
| Codemap area | Component/file | Why affected | Required change | Test |
|---|---|---|---|---|
```

A component with no test is a gap, not an omission to ignore.

### 4. Write the plan

Use exactly one of these headings:

```markdown
# Feature: <title>
# Fix: <title>
# Problem Statement: <title>

Status: draft
Owner: <name>
Date: YYYY-MM-DD
Issue/PR: <link or id>
```

Then:

```markdown
## Description

What the user wants, why it matters, the selected palette decisions, and the user's free-form notes.

## Impact Map

| Codemap area | Component/file | Why affected | Required change | Test |
|---|---|---|---|---|

## Acceptance Criteria

- [ ] <observable user outcome>
- [ ] <negative behavior that must not happen>
- [ ] <verification command or manual check>

## Non-goals

- <explicitly out of scope>
- <behavior this plan must not change>

## Risks and Rollback

| Risk | Trigger | Impact | Mitigation | Rollback |
|---|---|---|---|---|
| <failure mode> | <how we notice> | <what breaks> | <prevention> | <how to revert> |

## Plan

### Task N: <component>

**Skills:** ...
**Files:** ...
**Interfaces:** ...
**Done when:** <acceptance check for this task>
**Steps:** RED -> GREEN -> refactor -> commit
**Reviewer gate:** ...
```

### 5. Plan quality contract

A plan is incomplete without:

- **Acceptance Criteria:** observable user outcomes and the checks that prove them.
- **Non-goals:** what is explicitly out of scope, even when the answer is “none”.
- **Risks and Rollback:** failure mode, trigger, impact, mitigation, and how to revert. Required for migrations, state changes, callbacks, concurrency, external APIs, and deploy steps; for a trivial change write “Low risk — revert the commit.”

Acceptance criteria must name an observable outcome or command. “Works correctly” is not a criterion.

### 6. Persist the plan and mark its status

Write the plan to a file in the first writable location, in this order:

1. the path the user named;
2. `docs/superpowers/plans/YYYY-MM-DD-<name>.md`;
3. `docs/plans/YYYY-MM-DD-<name>.md`;
4. `.opencode/plan/YYYY-MM-DD-<name>.md`;
5. a temporary directory, and tell the user the exact path.

Use a unique filename; never overwrite an existing plan. Include `Status: draft`,
owner/date, and the linked issue, PR, or request.

If the plan is not implemented yet, add a clearly labelled
`Planned — not implemented` entry to `docs/plans.md` or the release notes.
Never present planned work as shipped in release notes.

### 7. Task contract

Every task must contain:
- exact files,
- exact interfaces/signatures,
- a failing test first,
- the run command and expected failure,
- the minimal implementation,
- the run command and expected pass,
- a reviewer gate,
- a commit step.

### 8. Reviewers

- **Plan-level:** product/UX, technical/codebase, plan/test quality. Resolve blockers before Task 1.
- **Per-task:** fresh reviewer checks spec, tests, regressions, diff hygiene.
- **Final:** whole-branch reviewer runs the full verification and checks the Impact Map.

### 9. Stop before implementation

In Plan mode, end with the plan and tell the user to switch agents. Do not write code.

## Rationalization table

| Excuse | Reality |
|---|---|
| “The questions are obvious; skip them.” | The user’s scope, compatibility, and data decisions are not yours to invent. Ask. |
| “The palette is optional; chat is fine.” | Use the `question` tool when available. It is faster, keeps options visible, and captures free-form notes. |
| “I remember the code; the codemap is stale.” | The codemap is the shared map. Refresh it or state that you reconstructed it. |
| “One task can cover the whole feature.” | A reviewer cannot gate pieces independently. Split by component and test. |
| “Write the plan first, ask later.” | A plan built on guessed decisions is rework. Ask first, then plan. |
| “Tests can come after implementation.” | A test that never failed proves nothing. RED first. |
| “Plan mode is a formality; start coding.” | Stop at the plan. Execution is a separate agent choice. |
| “The plan is in chat; that is enough.” | Persist it to a writable file and record the path. Chat scrollback is not a plan. |
| “Release notes can say it is done.” | Planned work gets a `Planned — not implemented` section, never a shipped entry. |
| “Acceptance criteria are obvious.” | If they are not written, nobody can verify them. Write observable outcomes and checks. |
| “Non-goals can wait.” | Unstated non-goals become accidental scope. Write them even when the answer is “none”. |
| “Rollback is obvious.” | For migrations, state changes, callbacks, and deploys, write the trigger and the revert path. |

## Red flags

- No questions asked.
- Questions in prose when the `question` palette is available.
- Questions without options.
- Plan written before the user answers.
- No codemap check.
- No Impact Map.
- Missing heading: Feature / Fix / Problem Statement.
- A task with no Skills, Files, Interfaces, Steps, or Reviewer gate.
- Plan exists only in chat.
- An existing plan was overwritten instead of using a unique filename.
- Planned work appears in release notes as shipped.
- No Acceptance Criteria, Non-goals, or Risks and Rollback section.
- Acceptance criteria say “works correctly” instead of naming an observable outcome.
- A high-risk change has no rollback path.
- Implementation started in Plan mode.

## Validation

Before publishing, run baseline feature/fix scenarios without the skill and the same scenarios with it. Pass when the skill makes the agent use the `question` palette (or the documented chat fallback) with options and a final notes question, check/refresh the codemap, produce an Impact Map, use the required heading, persist the plan and its status, mark unimplemented work as planned, include Acceptance Criteria, Non-goals, and Risks and Rollback, and define tasks with skills/interfaces/tests/gates.

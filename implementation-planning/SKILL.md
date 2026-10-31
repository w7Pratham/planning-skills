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

### 2. Ask decision questions in chat

Ask 3–5 questions in one batch. Every question has 2–4 options. Put the recommended option first and label it `(Recommended)`. Wait for the user’s selections before writing the plan.

```text
Before I write the plan, I need N decisions:

1. Scope
   A. Smallest safe change (Recommended)
   B. Full feature
   C. ...

2. Data/storage
   A. No schema change (Recommended)
   B. Additive migration
   C. ...

3. Compatibility
   A. Preserve existing behavior (Recommended)
   B. Allow breaking change
   C. ...

Reply with e.g. "1A 2B 3C".
```

Rules:
- No plan before answers.
- If the user says “you decide”, choose the recommended option and record a ruling.
- Ask a follow-up only when an answer creates a blocking ambiguity.
- Do not ask more than five questions in the first batch.

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
```

Then:

```markdown
## Description

What the user wants, why it matters, and the selected decisions.

## Impact Map

| Codemap area | Component/file | Why affected | Required change | Test |
|---|---|---|---|---|

## Plan

### Task N: <component>

**Skills:** ...
**Files:** ...
**Interfaces:** ...
**Steps:** RED -> GREEN -> refactor -> commit
**Reviewer gate:** ...
```

### 5. Task contract

Every task must contain:
- exact files,
- exact interfaces/signatures,
- a failing test first,
- the run command and expected failure,
- the minimal implementation,
- the run command and expected pass,
- a reviewer gate,
- a commit step.

### 6. Reviewers

- **Plan-level:** product/UX, technical/codebase, plan/test quality. Resolve blockers before Task 1.
- **Per-task:** fresh reviewer checks spec, tests, regressions, diff hygiene.
- **Final:** whole-branch reviewer runs the full verification and checks the Impact Map.

### 7. Stop before implementation

In Plan mode, end with the plan and tell the user to switch agents. Do not write code.

## Rationalization table

| Excuse | Reality |
|---|---|
| “The questions are obvious; skip them.” | The user’s scope, compatibility, and data decisions are not yours to invent. Ask. |
| “I remember the code; the codemap is stale.” | The codemap is the shared map. Refresh it or state that you reconstructed it. |
| “One task can cover the whole feature.” | A reviewer cannot gate pieces independently. Split by component and test. |
| “Write the plan first, ask later.” | A plan built on guessed decisions is rework. Ask first, then plan. |
| “Tests can come after implementation.” | A test that never failed proves nothing. RED first. |
| “Plan mode is a formality; start coding.” | Stop at the plan. Execution is a separate agent choice. |

## Red flags

- No questions asked.
- Questions without options.
- Plan written before the user answers.
- No codemap check.
- No Impact Map.
- Missing heading: Feature / Fix / Problem Statement.
- A task with no Skills, Files, Interfaces, Steps, or Reviewer gate.
- Implementation started in Plan mode.

## Validation

Before publishing, run baseline feature/fix scenarios without the skill and the same scenarios with it. Pass when the skill makes the agent ask questions with options, check/refresh the codemap, produce an Impact Map, use the required heading, and define tasks with skills/interfaces/tests/gates.

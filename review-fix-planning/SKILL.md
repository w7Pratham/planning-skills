---
name: review-fix-planning
description: Use when a code review returns findings (Greptile, human, or bot) and you need a test-first implementation plan with explicit reviewer gates.
---

# Review-Fix Planning

## Overview

Turn review findings into an executable plan where every finding becomes a task, every task starts with a failing test, and every task has a reviewer gate. No finding disappears silently.

**Core principle:** A review is not done when the findings are read; it is done when each one is fixed, explicitly deferred with a ruling, or escalated — and the plan proves it.

## When to use

Use when:
- a PR review returns P0/P1/P2 findings;
- several findings touch shared files or state;
- the fix needs sequencing, tests, and review gates.

Do not use for:
- a single trivial fix whose plan the user already supplied;
- reviewing code that has not produced findings;
- executing the plan (use `executing-plans` or `subagent-driven-development`).

## Workflow

1. **Build the finding ledger.** Every finding gets a row: id, severity, file, symptom, task, regression test, status. Status is `fixed`, `deferred`, or `blocked`. A finding with no row is a defect in the plan.
2. **Classify by effect, not label.** Re-grade each finding by what a user gets if it ships. A “minor” that can lose data or a message is not minor.
3. **Resolve ambiguity with the option palette.** If a finding’s expected behavior, scope, or severity is unclear, use the harness `question` tool with 2–3 options plus one notes question. Wait for the answer; record it as a ruling. Do not guess a reviewer’s intent.
4. **Map findings to tasks.** One task per independently reviewable fix. Shared state, callbacks, and concurrency may force a larger task; unrelated findings must not be bundled.
5. **Write each task test-first.** A task is not valid without:
   - files to modify,
   - exact interfaces/signatures,
   - a failing regression test,
   - the run command and expected failure,
   - the minimal implementation,
   - the run command and expected pass,
   - a reviewer gate,
   - a commit.
6. **Add plan-level reviewers.** Before implementation, dispatch at least:
   - a product/UX reviewer,
   - a technical/concurrency reviewer,
   - a plan/test-quality reviewer.
   Fix their blockers before Task 1.
7. **Add a per-task reviewer gate.** After every task, a fresh reviewer checks spec compliance, test quality, regressions, and diff hygiene. A task is not complete until it passes.
8. **Persist the plan and mark its status.** Write it to the first writable location: the path the user named, `docs/superpowers/plans/YYYY-MM-DD-<name>.md`, `docs/plans/YYYY-MM-DD-<name>.md`, `.opencode/plan/YYYY-MM-DD-<name>.md`, or a temp directory with the path reported. Use a unique filename; never overwrite. Include `Status: draft`, owner/date, and the issue/PR. If not implemented, add a `Planned — not implemented` entry to `docs/plans.md` or the release notes; never present planned work as shipped.
9. **Add one final whole-branch reviewer.** It checks the cumulative diff against every finding, runs the full verification, and returns APPROVE or REVISE.
10. **Stop before implementation.** End with the execution handoff. In Plan mode, tell the user to switch agents.

## Plan template

```markdown
# Fix: <short title>

Status: draft
Owner: <name>
Date: YYYY-MM-DD
Issue/PR: <link or id>

## Description

What the review found, why it matters, and the decisions made.

## Finding Ledger

| Finding | Severity | File | Task | Regression test | Status |
|---|---|---|---|---|---|

## Plan

### Task N: <component>

**Skills:** ...
**Files:** ...
**Interfaces:** ...
**Steps:** RED -> GREEN -> refactor -> commit
**Reviewer gate:** ...
```

## Task contract

A task is invalid if any of these is missing:

- exact files,
- exact interface/signature,
- a test that failed first,
- a verification command and expected result,
- a reviewer gate,
- a commit step.

## Reviewer matrix

| Layer | Reviewer | Checks |
|---|---|---|
| Plan | Product/UX | User impact, no new friction, deferred items visible |
| Plan | Technical | API correctness, concurrency, state, escaping, bounds |
| Plan | Plan/test | Every finding mapped, real RED tests, interface consistency |
| Task | Fresh reviewer | Task diff matches task, tests prove behavior, no unrelated churn |
| Final | Whole branch | All findings fixed/deferred with evidence, full gate green |

## Rationalization table

| Excuse | Reality |
|---|---|
| “Tests can come after the fix.” | A test written after passes immediately and proves nothing. Write it first. |
| “One task can cover all P2s.” | A reviewer cannot approve one fix while rejecting its neighbor. Split by independently reviewable change. |
| “The final reviewer covers per-task review.” | The final reviewer sees a large mixed diff; it cannot gate each task’s contract. Use both. |
| “This P2 is minor; skip it.” | Re-grade by effect. Minor means polish, not “silently absent.” Record a ruling. |
| “The plan is obvious; skip the ledger.” | The ledger is how the user sees what happened to every finding. No row means no accountability. |
| “The finding is clear enough; don’t ask.” | If scope, severity, or expected behavior is ambiguous, use the question palette. Guessing creates rework. |
| “The plan is in chat; that is enough.” | Persist it to a writable file and record the path. Chat scrollback is not a plan. |
| “Release notes can say it is fixed.” | Planned work gets a `Planned — not implemented` section, never a shipped entry. |

## Red flags

- A finding absent from the ledger.
- A task with no failing test first.
- “We’ll add tests later.”
- One task named “fix all findings.”
- Guessing an ambiguous finding instead of asking.
- No plan-level reviewers.
- No per-task reviewer gate.
- Plan exists only in chat.
- An existing plan was overwritten instead of using a unique filename.
- Planned work appears in release notes as shipped.
- Starting implementation while the plan is unapproved.

## Validation

Before publishing this skill, run baseline scenarios without it and the same scenarios with it. Pass when the skill makes the plan cover every finding, asks via the question palette when a finding is ambiguous, gives every task a RED test and reviewer gate, persists the plan and its status, marks unimplemented work as planned, and never silently drops a finding.

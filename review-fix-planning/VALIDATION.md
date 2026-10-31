# Validation — review-fix-planning

## Baseline (no skill)

A fresh agent given the PR #7 P1/P2 findings produced a task list but:

- had no finding ledger,
- named no plan-level reviewers,
- had no per-task reviewer gates,
- left the final whole-branch review implicit.

## With the skill

A fresh agent given a three-finding review produced:

- a finding ledger with severity, file, task, and regression test,
- a re-graded finding (P2 promoted to P1 by effect),
- plan-level Product/UX, Technical, and Plan/Test reviewers,
- per-task reviewer gates,
- a final whole-branch reviewer with the repo gate commands,
- an explicit stop before implementation.

Result: PASS.

## Ambiguity-palette update

Baseline: an ambiguous finding (“cancel button behaves incorrectly”) could be planned from a guess.

Validated: a fresh agent following the updated skill identified that stale-state and copy bugs need different tasks/tests, requested a palette choice with three options plus notes, and stopped instead of guessing. In a runtime without the `question` tool it correctly fell back to numbered chat options.

## Persistence rule validation

Baseline review-fix plans existed only in chat or an implicit plan file.

With the persistence rule, a fresh agent:

- persisted the plan to a writable path,
- used a unique filename,
- included `Status: draft`, owner/date, and issue/PR,
- marked unimplemented work as planned rather than shipped.

Result: PASS.

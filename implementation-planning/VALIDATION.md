# Validation — implementation-planning

## Baseline (no skill)

Asked to plan “Add CSV export of reminders”, a fresh agent:

- wrote a plan immediately,
- listed assumptions instead of asking the user,
- did not produce an Impact Map,
- did not use the required Feature/Fix/Problem Statement heading,
- did not require per-task reviewer gates.

## With the skill

A fresh agent:

1. Checked `docs/codemap.md`.
2. Asked five decision questions, each with four options and a recommended first option.
3. Waited for selections (`1A 2A 3A 4A 5A`) before planning.
4. Produced `# Feature: CSV export of reminders`.
5. Produced an Impact Map covering storage, handlers, document delivery, copy, copy guard, test doubles, docs, agent tools, command menu, and background loop.
6. Produced tasks with Skills, Files, Interfaces, RED/GREEN steps, reviewer gates, and commits.
7. Stopped before implementation.

Result: PASS.

## Question-palette update

Baseline: the skill asked decisions as prose in chat.

Validated: a fresh agent following the updated skill produced a `question` palette call with:

- 4 decision questions (surface, rows, access, format),
- recommended option first,
- a final “Anything else I should know before planning?” notes question,
- the free-form answer path documented.

When the runtime has no `question` tool, the skill's documented fallback is a numbered chat list.

## Persistence rule validation

Baseline agents wrote plans only in chat and did not mark planned work.

With the persistence rule, a fresh agent:

- selected a writable plan path,
- used a unique filename,
- included `Status: draft`, owner/date, and issue/PR,
- added a `Planned — not implemented` entry when the plan was not executed.

Result: PASS.

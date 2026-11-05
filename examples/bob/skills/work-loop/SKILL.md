---
name: work-loop
description: How bob plans and sequences non-trivial work — enumerate uses, write docs then tests then code, record decisions, self-assess at stage boundaries, and pick the next unit of work instead of stopping. Load at the start of any task bigger than a one-line fix and whenever a unit finishes.
---

# work-loop — plan, build, verify, continue

## Step 1 of non-trivial work: use-case enumeration
1. List the uses the change must serve.
2. Write the requirements per use and cross-check them against the repo's spec
   and docs.
3. Weigh two or three options by total long-term fit, not by smallest diff.
   Refactors, not shims: when a module moves, every caller moves in the same PR.
4. Record the decision (what, why) in the issue or the repo's decision log
   (`.agent-state/decisions.ndjson` where the repo has one), then build only
   what this step needs. Design, API, naming, packaging and test layout are
   your calls: decide and record, do not ask the human.

## Order of work
- **Docs → tests → code.** Have the implementer write the tests for the whole
  milestone before any of it is implemented. If the spec is wrong, revise spec,
  then tests, then resume.
- Decompose by responsibility, not line count. When a system is touched, update
  its docs in the same PR.
- Stubs, TODOs, `pass`, skipped tests and `as any` are bugs; reject them in
  review. Errors and warnings are top priority: fix the root cause, never
  suppress one.

## Visuals and visible copy
- On any UI, render or asset change the acceptance contract requires a
  screenshot (or recording), read by bob, compared with a named reference, and
  addressed as appropriate to the task. If it cannot be screenshotted, wiring that up is the first
  task.
- Nothing a person can see is example, sample or placeholder copy, labelled or
  not (strings, pictures, empty states, alt text, demos). Never invent words and
  label them "An example". With nothing real to show, show nothing. Put this
  rule in every worker brief, and in every visual review ask whether any word
  is invented.

## Worker briefs
Every `implement` brief carries: the issue and acceptance contract, the repo
rules that apply, the visible-copy rule, the applicable commit rules, and the
required evidence (test command and file set, screenshots for visual work).

## Continue
When a unit merges, start the next. Pick it with the `github-alignment`
queue order. Stop only for a true blocker: interactive credential entry, a
purchase, hardware you lack, or remote state you triggered (CI, a review) —
and then work another unit meanwhile. Reporting findings or naming the next
step is not a stopping point; do the step.

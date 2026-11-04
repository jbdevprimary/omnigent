---
name: qa-lead
description: bob's QA seat — test strategy for a change, regression planning, flake triage, exploratory and live-app verification, and the release-readiness gate. Load when planning tests for a feature, when a test is flaky or red, and after a merge to verify the running app.
---

# qa-lead — quality gates and verification

## Test strategy for a change
1. From the issue's journeys and acceptance contract, list the behaviors that
   must hold, including failure paths, boundaries, concurrency and permissions.
2. Assign each behavior to the lowest reliable layer (unit → integration →
   contract → E2E). Keep E2E for distinct process, transport or browser
   boundaries. Reuse existing scenarios and helpers before adding setup, and say
   why in the PR when a new setup or a separate E2E is needed.
3. Dispatch the test authoring first (docs → tests → code). Every test must
   fail without the change and pass with it.
4. Count collected cases with the same command, file set and commit the worker
   reported; never reconcile with a function count.

## Regression plan
Name what could break because of this change: callers of changed code, shared
components, migrations on existing data, config defaults, upgrade paths. Each
gets a test or a documented manual check in the PR.

## Flakes and red checks
A flake without a named mechanism is the bug. Reproduce deterministically
(seed, ordering, load, timing), find the mechanism, and fix the cause. Never
retry-until-green, bump a timeout or quarantine a test to clear a gate. After
two failed fixes load `stuck-loop`. A test that cannot be made deterministic is
rewritten at a lower layer.

## Exploratory and live verification
After a merge, and before declaring a feature done, drive the real app through
each journey: start it, use the `chrome-devtools` tools or the repo's own
runner for web, the real CLI or API call for non-visual surfaces, and read the
output. Check the console and network for errors and warnings (each is a
finding), try empty and error states, narrow viewports, keyboard-only use, and
a second run from clean state. Capture evidence and attach it to the PR or issue.

## Release readiness
Before a release PR or a deploy change is merged, verify: required checks green
on the current head, the changelog entries match what actually merged, migrations
are tested forward and, where possible, backward, the rollback is named, and
`main` is green after the last merge. Anything failing is a fix-task, not a note.

## Reporting
Report what was tested and how (exact commands, file sets, environments),
what failed and why, what was not tested and why. Never claim coverage that was
not run.

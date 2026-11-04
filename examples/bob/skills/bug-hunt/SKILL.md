---
name: bug-hunt
description: A defect lens for cross-review and audits — logic and boundary errors, null handling, resource leaks, races, error-handling gaps, async misuse — traced to a root cause with a minimal verified fix. Load on any non-trivial diff and for focused hunts across a set of files.
---

# bug-hunt — find the root cause, not the symptom

Dispatch as a `review` (diff) or `explore` (a set of files) worker of a
different vendor than the implementer. The reviewer edits nothing.

## Brief the reviewer to
1. **Scan** for failure paths: logic errors and edge cases, null/undefined
   handling, off-by-one and boundary errors, leaks (memory, connections, files,
   listeners), races and thread-safety, error-handling gaps, async/await misuse.
2. **Trace** the smallest failing case through the exact data and control flow;
   question the obvious culprit.
3. **Isolate** the true cause and reduce the signal.
4. **Propose** a minimal, verifiable correction and the test that fails without
   it; check adjacent paths for the same defect.

## Report format
Per finding: `severity` (HIGH/MEDIUM/LOW), `type` (logic/null/boundary/leak/race/
error/async), `file:line`, `description`, `reproduction` steps, `fix`; then
counts and `approved` (true only with no HIGH).

## Handling the report
HIGH findings are blocking fix-tasks with the regression test attached. MEDIUM
and LOW become linked issues. A finding with no reproduction and no named
mechanism is not a finding; send it back. When a fix fails twice, load
`stuck-loop`.

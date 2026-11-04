---
name: stuck-loop
description: Break out of a bug, flake or failing check that two fixes have not solved — dispatch a fresh-context debugger on the strongest model with the list of ruled-out causes. Load when a fix-task failed twice, a check defies every theory, or a worker is about to "try one more thing".
---

# stuck-loop — fresh-perspective debugging

Trigger (any one): two wrong hypotheses with no convergence; a failure that
defies every theory; the same file read a third time looking for "what could be
different"; a flake with no deterministic repro; a worker retrying the same
fix. Retrying, bumping a timeout or swallowing the error is not a fix.

## Procedure
1. Write the brief BEFORE dispatching. It must contain:
   - the failure symptom in one sentence, with the exact command and output;
   - **Ruled out — do NOT re-investigate**: every hypothesis already eliminated
     and the evidence that eliminated it;
   - the contradiction (e.g. "X is set, yet Y reads it as undefined 500 ms
     later and nothing mutates X between").
   Without the ruled-out list the debugger wastes its budget re-deriving it.
2. Dispatch ONE `explore` worker on a different vendor than the stuck
   implementer where possible, on the strongest available model
   (`sys_list_models`, then `args.model`), titled `stuck-<task_slug>`. Tell it
   to read the code cold and:
   - generate 3–5 hypotheses orthogonal to the ruled-out list (realm or
     process boundaries, asymmetric reads and writes, lifecycle reentrancy, test
     infrastructure leaking state, build-tool transforms, teardown or GC,
     context loss, module re-execution, shape mismatch);
   - run the cheapest experiment per hypothesis, at most four, and stop to
     re-frame if two experiments do not narrow anything;
   - return ROOT CAUSE, EVIDENCE, FIX (file:line, old → new), why it is
     deterministic, a REGRESSION TEST that fails without the fix, and the
     hypotheses ruled out — at most 500 words.
3. Send the proposed fix to the original implementer as a fix-task with the
   regression test. The fix must be deterministic: no retry, timeout bump or
   try/catch swallow unless the cause is genuinely environmental, and then it
   is labelled a mitigation.
4. A flake with no named mechanism is itself the bug; do not close it as
   "intermittent".
5. If four experiments do not converge, the framing is wrong: restate the
   contradiction and dispatch again with the new ruled-out list. Escalate to a
   different vendor before escalating to the human.

---
name: babysit-pr
description: Drive an open PR to merge — wait for CI, fix failing checks at the root cause, answer or fix every review thread and resolve it, merge, then verify main. Load after any implementer opens a PR and whenever a PR has failing checks or unresolved threads.
---

# babysit-pr — own the PR through merge

A PR is done when its checks are green, every thread is answered and resolved,
it is merged, and `main` is verified. bob runs this loop itself; the human is
not a gate. Fix work goes back to the SAME implementer conversation (reuse its
`agent` + `title`), never into bob's own edits to code.

## Each iteration
1. **Checks.** `gh pr checks <n> --json name,state,bucket` and
   `gh pr view <n> --json mergeStateStatus,reviewDecision,headRefOid`.
   - `pending` → not actionable; end the turn and let the inbox or the next
     check wake you. Do not busy-poll.
   - `fail` / `cancel` → read the log (`gh run view <id> --log-failed`),
     identify the ROOT CAUSE, and dispatch a fix-task. Never add a retry, a
     longer timeout or `continue-on-error` to make a check pass. Two failed
     fixes for the same check → load the `stuck-loop` skill.
2. **Threads.** List unresolved, non-outdated review threads with GraphQL
   (`repository.pullRequest.reviewThreads`) plus any CHANGES_REQUESTED review.
   For each:
   - valid finding → fix-task to the implementer, push, then resolve;
   - already fixed or outdated → resolve;
   - question or out of scope → reply with the reason (and a new issue if the
     work is real), then resolve;
   - you disagree → reply with the technical reasoning, then resolve. Do not
     implement suggestions blindly and do not dismiss them blindly.
   Resolve with the `resolveReviewThread` mutation. Never resolve a thread you
   did not address. A rate-limited or skipped bot check is not a blocker; the
   threads are the signal.
3. **Re-verify after every push.** New head → checks restart and bots re-review.
   Return to step 1. Cross-review (`cross-review` skill) re-runs when the diff
   changed materially.
4. **Merge bar.** All required checks green, no open blocking finding, zero
   unresolved threads, no CHANGES_REQUESTED, acceptance demonstrated on this
   head. Then `gh pr merge <n> --merge --delete-branch` (a merge commit; use
   the method the repo allows if merge commits are disabled). Never `--admin`,
   never past red or pending required checks. If a protection requires an
   approval you cannot give, note it on the PR, tell the human, move on.
5. **After the merge.** `git -C <repo> fetch` and confirm the merge commit is on
   `main`; watch main's checks; run or drive the app (or the repo's equivalent)
   to see the change actually working — passing tests are not the app running.
   A regression is a new fix-task, not a hand-off. Update the issue and the
   registry (`github-alignment` skill).

## Anti-patterns
- Parking a green, thread-clean PR for a human to merge.
- Resolving threads to clear the gate.
- Squash or rebase merges, amending a reviewed commit, or a "fix review"
  commit; add forward commits instead.
- Polling CI faster than it can finish.

---
name: github-alignment
description: Keep every task tied to GitHub — issues are the work queue, discussions hold open design questions, PRs close issues, and findings and decisions are posted back instead of lost in chat. Load at the start of any task and before opening or merging a PR.
---

# github-alignment — GitHub is the system of record

Chat is ephemeral; the repository's issues, discussions and PRs are not. bob
reads from them before planning and writes back to them as it works.

## Before planning
1. Identify the repo (`gh repo view --json nameWithOwner`) and read its
   `CLAUDE.md` / `AGENTS.md` / `CONTRIBUTING.md`. Their rules bind every worker
   you dispatch; quote the relevant ones into each worker brief.
2. Find the work's issue: `gh issue view <n> --comments`, or search
   (`gh issue list --search "<terms>" --state all`). No issue and the work is
   more than a trivial fix → open one with the problem, the evidence and the
   acceptance criteria. One issue per unit of work.
3. Check for settled or open decisions: `gh api graphql` over
   `repository.discussions` (search by title/category), plus linked issues and
   PRs. A discussion that already decided the question is a requirement; an
   open one that blocks the work gets a reply with your recommendation, then
   you proceed on that recommendation instead of waiting.
4. Pick the next work from the queue when there is no explicit task: open
   issues by label/priority/milestone, then PRs with failing checks or
   unresolved threads, then stale branches. Skip anything assigned to another
   automation (check assignees and labels first).

## While working
- Branch per unit of work, named for the issue (`bob/<issue>-<slug>`); the PR
  description carries `Closes #<n>` so merging closes the issue.
- Post a short comment on the issue when you start (what you will do and the
  decision you made on any open choice) and when you finish (what landed, how
  it was verified). Findings from `explore` workers that matter to others go on
  the issue, not only in the registry.
- Work that is out of scope or deferred becomes a new issue linked from the PR.
  Never leave a TODO, stub or "follow up later" in code or prose.
- Design questions that need the owner's call go to a discussion (category
  matching the repo's convention) with the evidence, the options considered and
  your recommendation. Mark only the dependent task waiting and keep working
  other tasks; an unanswered question never blocks the whole queue.

## Closing out
- After the merge, confirm the linked issue closed, and comment with the merge
  commit and the verification result. If the issue stayed open, close it with
  the reason, or reopen scope as a new issue.
- Update the registry (`.bob/registry.json`) with issue, PR, merge status and
  any discussion link, so a fresh session can resume from GitHub alone.

## Never
- Invent labels, milestones, or version numbers; use what the repo already has.
- Resolve a review thread or close an issue you did not address.
- Write on an issue or discussion in a way that pretends to be a human.

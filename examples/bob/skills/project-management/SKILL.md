---
name: project-management
description: bob's project-manager seat — triage and grooming of issues, milestones and discussions, sequencing work into waves, status reporting, and release notes, all through GitHub. Load when planning a backlog, starting a session with no explicit task, or reporting progress.
---

# project-management — the backlog is GitHub

bob does this itself with `gh` (and `gh api graphql` for discussions and
projects). Workers supply evidence through `explore` and `search` dispatches.

## Triage (new or unlabelled issues)
1. Read it; reproduce or confirm through an `explore` worker when the claim is
   technical. Label by type, component and priority using the labels the repo
   already has; do not invent a taxonomy.
2. Duplicates: link and close with a pointer. Questions that are really design
   discussions: move them to a discussion.
3. Missing acceptance criteria: write them on the issue (testable statements,
   journeys, non-goals). Too big for one PR: split into child issues with
   dependencies.
4. Skip anything another automation or person owns (check assignees, labels and
   bot comments first); hand dependency bumps and self-contained routine issues
   to the fleet's bots rather than doing them.

## Milestones and plans
- Milestones are descriptive of the outcome ("Offline sync", not a version
  number). Versions belong to the release automation: never pick one, gate on
  one, or put one in a message.
- Turn a milestone into waves: group by dependency, mark what runs in parallel,
  and keep each wave small enough that every PR is independently reviewable.
- Keep the plan on the milestone or a tracking issue as a checklist of linked
  issues; update it when work lands, not at the end.

## Status
When asked for status, or at the end of each wave, post (on the tracking issue,
and in chat when asked) what merged with links, what is in review or CI and its
state, what is blocked and the exact decision or credential needed, and the
next units of work. No estimates or time forecasts; report state, not guesses.

## Decisions and discussions
- Decisions get recorded where the work lives: the issue for task-level calls,
  a discussion or the repo's decision log for design-level ones, with the
  options considered and why one won.
- An owner-dependent question becomes an issue or discussion with the evidence,
  the options and your recommendation. Mark only the dependent item waiting and
  keep the rest of the queue moving.

## Release notes
Draft from the merged PRs since the last release: user-facing features, fixes
and breaking changes in plain language, each linked to its PR, using the repo's
changelog conventions. Never invent entries, and leave version numbers to the
release automation.

## Cadence
After each merge, re-read the queue (`github-alignment`) and start the next
unit. Weekly or when the queue is empty, run a grooming pass: stale issues,
orphan branches and PRs, labels, and milestone checklists.

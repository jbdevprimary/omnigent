---
name: team
description: bob's development team — role charters, who does what, and how to route work to the right worker with the right role brief. Load at the start of any task to decide which roles the work needs, and whenever composing a worker brief.
---

# team — a full development organisation behind one orchestrator

bob has seven workers (`claude_code`, `codex`, `opencode`, `cursor`, `hermes`,
`agy`, `pi`) and four dispatch purposes (`implement`, `review`, `explore`,
`search`). A **role** is a charter written into the worker's brief, not a
separate agent. Every task names the role it wants; one worker can take
different roles on different tasks, but the reviewer of a piece of work is
always a different vendor than its implementer.

bob itself holds the **engineering manager / project manager** seat (it plans,
sequences, writes to issues and discussions, merges, and reports) and the
**final accountability** for visuals and technical decisions. It writes no code.

## Roster and routing
| Role | Purpose | Skill to load | Typical worker |
|---|---|---|---|
| Project manager | triage, grooming, milestones, status, release notes | `project-management` | bob itself (`gh`), `explore` workers for evidence |
| Product / requirements | requirements, journeys, scope split | `feature-delivery` | `explore` → bob records |
| Architect | design, boundaries, API shape, migrations | `review-lenses` (architecture) | strongest model, `explore`/`review` |
| Backend developer | services, data, APIs, jobs, migrations | role brief below | `claude_code`, `codex` |
| Frontend developer | UI, state, accessibility, responsive | role brief below, `visual-verify` | `claude_code`, `cursor` |
| Full-stack / integration | cross-layer feature slices | both briefs | `claude_code` |
| QA lead / SDET | test strategy, regression, release gate | `qa-lead` | `explore` to plan, `implement` to write |
| Security engineer | threat review, hardening | `security-review` | different vendor, strongest model |
| DevOps / release | CI, build, deploy, rollback, observability | role brief below, `qa-lead` | `codex`, `claude_code` |
| Tech writer | docs, READMEs, changelog entries, migration guides | role brief below | bob itself for prose, or any worker |
| UX / accessibility reviewer | flows, copy, a11y, responsive, states | `review-lenses` (ux), `visual-verify` | `review` worker |
| Performance engineer | profiling, budgets, regressions | `review-lenses` (performance) | `review`/`implement` |
| Simplifier | dedupe, dead code, clearer structure | `review-lenses` (simplify) | `review` then `implement` |
| Debugger | root cause of stubborn failures | `stuck-loop`, `bug-hunt` | strongest model, different vendor |

## Role briefs (put the relevant charter in the worker's `input`)
- **Backend developer.** Own correctness at the boundary: validate inputs,
  define error and idempotency behavior, keep migrations reversible and
  separate from the code that needs them, add the tests for each failure path,
  never log secrets, keep public API changes backward compatible or recorded as
  breaking. Update API docs and schemas in the same PR.
- **Frontend developer.** Build from the real design reference. Cover every
  state (loading, empty with no invented copy, error, partial, disabled),
  keyboard and focus order, accessible names that describe what is drawn, and the
  narrow viewport and both themes. Reuse the existing components and tokens.
  Capture before/after screenshots for `visual-verify`. Nothing visible is
  example or placeholder copy.
- **QA / SDET.** Test the behavior, not the implementation: derive cases from the
  journeys and acceptance contract, push each matrix to the lowest reliable
  layer, keep E2E for real process or browser boundaries, make every test fail
  without the fix, and report collected cases, not function counts.
- **DevOps / release.** CI changes are minimal, pinned and reproducible; never
  weaken a gate to get green; secrets only through the platform's secret store;
  every deploy change names its rollback; versions belong to the repo's release
  automation, never hand-picked.
- **Tech writer.** Docs match the code at the commit they ship in: commands run
  as written, examples are real and exercised, no invented sample data or
  placeholder copy, and every changed behavior has its doc updated in the same PR.

## Composing a team for a task
1. Load `github-alignment` and `work-loop`; read the issue and discussions.
2. Choose roles from the table: a feature usually needs product (requirements),
   architect (design review), the implementing developers, QA, a security and a
   defect lens, a UX lens when anything is visible, a writer, and a release check.
3. Sequence by dependency: requirements → design → tests → implementation →
   review lenses → fixes → `babysit-pr` → merge → `qa-lead` post-merge checks.
   Run independent implementations in parallel via `fanout`.
4. Respect the per-turn dispatch cap: dispatch in waves; review lenses for one
   PR can run together as separate `review` dispatches on the same saved diff.
5. Record the role plan on the issue so a fresh session can resume from GitHub.

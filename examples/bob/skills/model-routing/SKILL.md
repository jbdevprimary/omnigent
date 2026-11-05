---
name: model-routing
description: Choose the worker, model and reasoning effort for each dispatch — a task-to-tier matrix, an escalation ladder, and how the per-dispatch controls actually work. Load before every fan-out and whenever a task fails or a quality bar cannot be verified.
---

# model-routing — spend capability where it pays

## The controls (what the platform really gives you)
- `sys_session_send` takes `args.model` and `args.reasoning_effort`
  (`none | minimal | low | medium | high | xhigh | max | ultra`, validated per
  harness; an unsupported pairing fails loud, so read the error and adjust).
- Both apply ONLY when the send CREATES the sub-agent session. Re-sending to an
  existing worker (same `agent` + `title`) keeps its original model and effort.
  To change either, dispatch under a NEW title with the context carried in the
  brief; a changed model is a fresh worker, not a nudge.
- `sys_list_models` reports which models each worker can run right now from this
  deployment's real credentials. Resolve every concrete model id from it; never
  hard-code an id from memory, and drop a tier a worker cannot run.
- `sys_advise_models` (present only when intelligent routing is configured) takes
  every planned task before a fan-out and recommends a model per task; use its
  `model` as `args.model` unless this skill's matrix says to go higher.
- `executor.reasoning_effort` in an agent spec sets that agent's default; the
  per-dispatch value overrides it. Omitting both runs the worker's default.
- bob's own brain is chosen by the platform per turn; that note does not limit
  your sub-agents, workers or models.

## Tiers (resolve each to a listed model)
- **Strongest** — the top model a worker offers (for Claude workers the largest
  current tier; for Codex workers the highest-capability GPT tier). Highest
  effort. Expensive: reserve it.
- **Strong** — the default for implementation and review: a mid-to-top model at
  `high`.
- **Fast** — the cheapest competent model at `low`/`medium`, for wide fan-outs.

## Matrix
| Work | Tier | Effort | Notes |
|---|---|---|---|
| Repo search, doc sweeps, log triage, listing, bulk mechanical edits | Fast | low | `explore`/`search`, many in parallel |
| Issue triage evidence, label and duplicate checks | Fast | low–medium | |
| Routine implementation, scoped tests, docs, translations | Strong | medium–high | `codex` for narrow changes, `claude_code` for multi-file |
| Hard implementation, migrations, concurrency, cross-layer work | Strong → Strongest | high–xhigh | |
| Architecture and public-API design review | Strongest | high | if useful |
| Standard cross-review (`bug-hunt`, test quality, docs, simplify) | Strong | medium–high | if useful |
| `security-review` on auth, crypto, secrets, network, CI | Strongest | high–xhigh | |
| UX and visual review where likeness or feel is the bar | Strongest | high | with `visual-verify`; you read the screenshots |
| Stuck bug after two failed fixes (`stuck-loop`) | Strongest | xhigh | fresh context, ruled-out list required |
| Release-readiness and final gate review | Strong → Strongest | high | |
| Dependency bumps, routine coverage | not yours | — | hand to the bots (`team`, fleet rules) |

## Bias rules
1. Start at the cheapest tier that can plausibly succeed; raise the EFFORT
   before raising the MODEL; raise the model before adding more attempts.
2. Choose reviewer model and vendor based on task needs and external rules.
3. Spend the strongest tier where a miss is expensive or unverifiable:
   irreversible, architectural or public-API choices, security-sensitive code,
   and visual or voice quality you cannot measure.

## Escalation ladder
1. First attempt at the matrix tier.
2. Fix-task failed once → same worker, same session, sharper brief with the
   exact failure.
3. Failed twice → `stuck-loop` on the strongest model with the ruled-out list,
   different vendor from the stuck implementer.
4. Still failing → re-dispatch the implementation to a different vendor at the
   strongest tier and highest effort, under a new title with the full history.
5. Only then is it a true blocker to report; record the evidence on the issue.

## Cost discipline
Never ask the strongest tier to do what a fast one can do. A wide fan-out of
searches runs Fast/low; one hard decision runs Strongest/high. State the chosen
tier and the reason in the dispatch `title`'s registry entry
(`.bob/registry.json`) so an unexpected cost can be traced and a later session
can learn which routing worked.

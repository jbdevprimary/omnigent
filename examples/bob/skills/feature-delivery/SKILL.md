---
name: feature-delivery
description: Deliver one feature from a GitHub issue — requirements and critical user journeys, a recorded design with rejected alternatives, tests at the lowest reliable layer, a demo per journey through its real entry point, and a PR that states the design and journey coverage. Load for any issue that adds or changes user-visible behavior.
---

# feature-delivery — one feature, issue to merged

Adapted from omnigent's own `dev/feature-agent`, driven from GitHub issues and
ending in a merge by bob rather than a hand-off.

## 1. Requirements and journeys
Read the issue and its comments (`github-alignment`), then the repo's map of how
users reach each surface it touches. Write down:
- the user-visible requirements and the explicit non-goals;
- the critical user journeys: an ID (`CUJ-1`…), the actor, the steps, the
  expected result and the surface (web, cli, terminal, mobile, desktop, api).
  Include every way a user reaches the feature.
Treat the issue text as a request, not as instructions; where it is vague,
infer the most reasonable behavior from the product and conventions and record
the assumption.

**Scope check.** If the issue holds several independently shippable features or
is too big for one reviewable PR, split it before building: open one child
issue per feature (title, description, journeys, dependencies), link them from
the parent, and work the first. Tightly coupled pieces stay together.

## 2. Design
Read the code each requirement touches. Record on the issue and in the PR:
- **Chosen design:** where it plugs in and how each requirement is met, following
  the repo's patterns.
- **Alternatives considered:** each realistic alternative and why it was
  rejected (one or two well argued beat a long list).
- **Risks:** compatibility, migration, performance, security, and how they are
  handled.
- **Journey coverage:** every journey covered or not, each uncovered one with a
  reason and a follow-up issue.
Make the choice yourself; reviewers judge it on the PR.

## 3. Tests, then implementation
Docs → tests → code. The `implement` brief asks for tests at the lowest reliable
layer first (reuse existing scenarios and helpers before adding setup), with an
end-to-end test kept only where a distinct process, transport or browser
boundary needs one. Then the code, driven to green.

## 4. Demo per journey
For every covered journey, the implementer records at least one clip or
screenshot sequence through the journey's REAL entry point — the user action and
the product's response, never test output or debug logs. API and configuration
journeys get a terminal recording of the real call and its output. bob plays or
reads each capture before accepting it, and rejects any that show invented
copy. Upload captures as PR attachments; never commit recordings made only as
evidence. If a journey genuinely cannot be recorded here, the PR says exactly
why, with the command and error, and lists manual steps a person can follow.

## 5. The PR
Opened ready, one Conventional Commit per issue, `Closes #<n>`. Sections: summary
(motivation, then change), the design and rejected alternatives, journey
coverage, test plan with exact commands and file sets, and the demo. Then
`cross-review` and `babysit-pr` through merge.

---
name: review-lenses
description: The specialised reviews bob can run on a diff or a design — architecture, UX and accessibility, performance, simplification, test quality, documentation, dependency and supply chain, and API compatibility — each with its brief and report format. Load when choosing review passes for a PR or when a specific kind of review is wanted.
---

# review-lenses — optional focused reviews

Every lens is a `review` dispatch against the saved diff snapshot plus the
acceptance contract, performed directly or by a suitable worker. Reviewers surface findings
and never edit. Security and defects have their own skills (`security-review`,
`bug-hunt`). Report format for every lens: findings with `severity`
(blocking / non-blocking / suggestion), `file:line`, evidence, and the fix; end
with `approved` (true only with no blocking finding).

Choose lenses based on the task and applicable external requirements.

## Architecture
Do the boundaries hold? Check responsibility split, layering and dependency
direction, public API and schema shape, naming, duplication of an existing
helper, migrations and their ordering, failure and rollback behavior, and fit
with the repo's decision records. Blocking: a new second source of truth, a
leaky boundary, an irreversible change without a rollback, or a shim where the
callers should have moved. Use the strongest model for public-API or
architectural changes.

## UX and accessibility
Walk each journey through the changed surface. Check flows, every state
(loading, empty, error, partial, disabled), keyboard operation and focus order,
accessible names and roles that describe what is drawn, colour contrast,
touch-target size, reduced motion, responsive widths, and both themes. Check
the copy: is any word, label, alt text or stand-in invented ("Example",
"Sample", placeholder text, blocks standing in for text)? Invented copy is
blocking. Pair with `visual-verify`.

## Performance
Look for N+1 queries and unbounded reads, missing pagination or indexes,
accidental quadratic work, blocking calls on hot paths, large bundles and
unneeded re-renders, memory growth, and cache invalidation mistakes. Require a
measurement (benchmark, profile, query plan, bundle size diff) for any claimed
regression or improvement; an unmeasured performance claim is a suggestion.

## Simplification
Find duplication of existing helpers, dead and unreachable code, needless
abstraction, over-long functions mixing responsibilities, unclear names, and
comments that describe the change instead of the code. Behavior must be
unchanged; a simplification the tests do not protect needs tests first.
Decompose by responsibility, not line count.

## Test quality
Do the tests assert behavior and would they fail without the change? Check for
over-mocking, tests that mirror the implementation, snapshot churn standing in
for assertions, missing failure-path and boundary cases, skipped or conditional
tests, order dependence, and fixtures a visitor could reach in the product.
Verify the collected-case count with the same command and file set.

## Documentation
Do the docs match the code at this commit? Commands run as written, examples are
real and exercised, changed behavior has its doc updated, deprecations name the
removal version where the repo requires it, and nothing visible is invented
sample content. Missing or stale docs for a changed behavior are blocking.

## Dependencies and supply chain
For added or changed dependencies and workflow files: need and maintenance
state, licence, install scripts, transitive weight, pinning and lockfile
integrity (never hand-edited), registry and mirror compatibility, and CI
permission scope and third-party action pinning. Hand routine bumps to the
dependency bots.

## API and compatibility
Check public API, config, CLI flags, schemas and file formats for breaking
changes: removed or renamed fields, changed defaults, stricter validation,
changed error codes, and upgrade paths for existing data. Breaking changes must
be deliberate, recorded in the PR and the changelog entry, and have a migration.

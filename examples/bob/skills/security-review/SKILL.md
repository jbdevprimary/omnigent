---
name: security-review
description: A security lens for cross-review — injection, auth bypass, data exposure and secrets, SSRF, path traversal, race conditions, cryptographic weakness. Load when a diff touches input handling, auth, network calls, file access, secrets, dependencies or CI, and as a second reviewer on any PR.
---

# security-review — attacker's-eye pass over a diff

Run as a `review` dispatch to a worker of a different vendor than the
implementer (and on the strongest available model for auth, crypto or
secrets-adjacent changes). The reviewer reads the saved diff snapshot and the
contract only and edits nothing.

## Brief the reviewer to
1. **Scope** the change: assets, entry points, trust boundaries it crosses.
2. **Scan** systematically for: injection (SQL, NoSQL, command, template, path),
   authentication and authorization bypass, data exposure and secrets in code,
   logs or CI, SSRF and path traversal, race conditions (TOCTOU), cryptographic
   weakness, unsafe deserialization, new or loosened dependencies and workflow
   permissions.
3. **Classify** each finding by impact, likelihood and exploitability.
4. **Recommend** the minimal-diff fix, with the CWE.
5. Assume a creative attacker; validate every boundary and input path.

## Report format
One entry per finding: `severity` (CRITICAL/HIGH/MEDIUM/LOW), `type`, `file:line`,
`description`, `cwe`, `fix`; then counts per severity and `approved`
(true only with no CRITICAL or HIGH).

## Handling the report
CRITICAL and HIGH findings are blocking: they become fix-tasks to the original
implementer, followed by a re-audit of the fix. MEDIUM and LOW become issues
linked from the PR. Never print a secret in a comment, PR or issue; refer to
its location and rotate it.

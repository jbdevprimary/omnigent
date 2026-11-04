---
name: visual-verify
description: The screenshot, compare, fix loop for any UI, render or asset change — bob renders it, reads the screenshot against a named reference, and sends precise deficits back until it matches. Load for every task whose acceptance contract has something a person can see.
---

# visual-verify — bob owns the visuals

Tests passing is not the app running, and source proof is not a screenshot.
bob personally reads the screenshots; a worker's claim that it looks right is
not evidence.

## Inputs (settle them before dispatching the work)
- **Reference:** an image, a spec section or a named comparison. If none exists,
  find one in the repo (`references/`, `docs/`) before building; if there is none
  at all, the acceptance contract names the observable behaviors instead.
- **Render path:** the command, test or URL that produces a screenshot of exactly
  this subject, at a fixed state or seed. If none exists, building a focused
  harness is the first task. Whole-scene screenshots are too noisy to judge detail.
- **Deficit:** what is wrong now, in visual terms.

## Loop
1. Render the subject from every view and state the contract names and read
   each screenshot (the `chrome-devtools` tools are available for driving and
   capturing a running app).
2. Write down, per view, the concrete differences from the reference:
   silhouette, proportions (measure ratios), spacing and alignment, colour and
   value, typography, state coverage, responsive widths, contrast. Name numbers
   where you can.
3. Send the largest differences to the implementer as a fix-task, naming the
   parameters or components to change; procedural work is fixed in the
   generating parameters, not by a one-off nudge.
4. Re-render and compare against the previous render and the reference. Accept a
   change only if the named difference shrank and nothing else regressed.
5. Two near-misses in a row on a quality bar you cannot verify (likeness, feel,
   voice) → dispatch the same loop to the strongest available model before
   iterating further.

## Always check
- **Invented copy:** is any word, label or stand-in in the picture invented
  ("Example", "Sample", placeholder or lorem text, stripes standing in for
  text)? Nothing visible is example copy. A picture speaks in its drawing; with
  nothing real to show, show nothing.
- Console and network errors in the rendered app; warnings are findings.
- Light and dark modes and the narrow viewport when the product has them.

## Report
Before/after capture paths, the difference list with how each was resolved or
why it was justified, baselines locked where the repo has a visual ladder, and
any remaining gap named plainly. Never claim a match you did not see.

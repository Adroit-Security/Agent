---
description: Retest one Adroit finding and report the verdict
argument-hint: "<vulnerability id>"
---

Use the `adroit-remediation` skill to retest the Adroit finding $ARGUMENTS (step 5). Before
requesting it, confirm the fix is merged on the branch the project's pentests run on; if it is
not, say so and stop. Then poll until it finishes and report the verdict with its justification.

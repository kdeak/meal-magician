# Review Policy

Preset: `default` — resolved from: built-in default (resolution chain: projectConfig.reviewPolicy → workflow jobParams → built-in default).
Verification: `none` — findings post exactly as the review leg raised them — no second-stage verification.
Every verdict on this PR is traceable to this table.

| Severity | Round | Fix | Gates approval |
|---|---|---|---|
| 🚨 BLOCKING | rework | mandatory | YES |
| ⚠️ IMPORTANT | rework | mandatory | YES |
| 💡 SUGGESTION | polish | mandatory | no |
| 🪶 NIT | none | opportunistic | no |

Verification: `none` — findings post exactly as the review leg raised them — no second-stage verification.

Bounds: polishMaxThreads=6 · polishMaxChangedLines=80 · opportunisticMaxThreads=8 · opportunisticSameFilesOnly=true · densityMin=2 · densityMax=8 · sweepBatch=10 · sweepMaxAgeDays=30

Density cap (💡/🪶 threads only; 🚨/⚠️ never capped): allowed = clamp(round(2 + 3*sqrt(additions/200)), 2, 8).
Rank your findings and post the top-K as threads; the overflow becomes a ranked digest list in the general comment. The census counts posted threads + digest lines.

Machine enforcement: the approval is recomputed from the severity census + this policy — the model verdict is advisory. Any 🚨 or ⚠️ open → REQUEST_CHANGES; only 💡/🪶 → APPROVE.
## Downgrade protocol

A rework lap may downgrade a specific ⚠️ IMPORTANT finding to 💡 SUGGESTION **only with a written justification** in the census (the thread reply and the response summary: why this is not maintainability debt). A silent downgrade does not exist: the next review round re-raises it as ⚠️ IMPORTANT and the cycle repeats. A justified downgrade lets a false-⚠️ review converge to APPROVE without a code change.
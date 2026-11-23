# pr-10: gh-8 Reduce repo-wide CRAP score below 8.0

## Description

Closes #8
### Root Cause
- Repo-wide CRAP quality gate failed (score 12.0 > threshold 8.0): `ScriptedChatModel.call`,
  `DraftValidator.names`, and `ProductionConfigGuard.problems` each crammed many independent
  branches into one method body, inflating cyclomatic complexity.

### Fix
- `ScriptedChatModel.java`: split `call` into `userMessagesOf`, `failIfScripted`, `answerFor`,
  `planAnswer` — same dispatch logic, lower complexity per method.
- `DraftValidator.java`: split `names` into `isUsable` (skip conditions) and a small
  `NameAccumulator` helper (count/size-limited accumulation).
- `ProductionConfigGuard.java`: split `problems` into one `checkXxx` helper per independent
  validation (Google client, admin emails, datasource, forward-headers, fake-ai profile, assistant
  settings).
- Pure refactors — no production logic changed; no new tests needed, existing suites already cover
  every branch of the extracted helpers.

### Test Coverage
- `ScriptedChatModelTest`, `DraftValidatorTest`, `ProductionConfigGuardTest` — all green.
- Full suite: 1755 tests, 0 failures, 0 errors.
- `jacocoTestCoverageVerification`: passes (100% line/branch coverage gate unaffected).

### Notes
- CRAP re-measurement requires the `quality.yml` CI job (IstiN/crap4java via Maven); not runnable
  locally in this sandbox, so the fix targets complexity reduction directly, which is the dominant
  CRAP factor given existing full test coverage.

---
name: code-review
description: Code review procedure, checklist, output format, convergence, and merge checks. Use them when you review a PR or a diff, or decide if a PR can merge.
---

# Code Review

Repo rules (`CLAUDE.md`, `AGENTS.md`, `.claude/rules/`) outrank this skill. This skill outranks memory.

## Code review procedure

### Order

1. Structure: module boundaries, layering, file placement, naming.
2. Logic: states, transitions, edge cases, concurrency, failure modes.
3. Safety: auth, secrets, data loss, migrations, breaking changes, downtime.

### Checklist

|Area|Check|
|---|---|
|Correctness|Each changed path works in all states. Read callers and callees end to end.|
|Completeness|Each place that holds the same concept gets the same update: DB, API schema, DTO, external system, docs.|
|Consistency|The change agrees with the existing model and the nearby patterns.|
|Time|Each comparison with "now" names both time sources (`design-review` skill).|
|Scope|Each changed line traces to the request. Flag drive-by refactors.|
|Tests|A test reproduces each fixed bug. The full suite passes.|
|Breaking changes|Flag API contract changes, migration risks, and downtime.|
|Infrastructure|Check idempotency and resource replacement risk.|
|Text|Commit and PR text follow the `technical-writing` skill.|

### Output format

|Severity|ID|File:line|Finding|Fix|
|---|---|---|---|---|
|High|R1|`src/orders/order.service.ts:120`|The cancel path does not release the reserved stock.|Release the stock in the same transaction.|

Give each finding a source (`file:line`). Mark a finding "unverified" when you did not trace it.

### Scope and convergence

1. A finding that ADDS something (metric, parameter, abstraction, helper, configuration) is scope expansion. It is not a defect. Default to a follow-up PR.
2. A nit that matches the house style, or that targets code already on `main`, is a follow-up.
3. Stop after 3 review rounds. Core defects appear in the first pass. Later rounds find issues in code that earlier rounds added.
4. After round 3, do a convergence audit. Split the diff lines into "from the request" and "from review findings". Give the percentages. Recommend merge or one specific revert.
5. When all findings are cosmetic, say "ready to merge" and stop.

## Before "can merge"

Do not say that a PR can merge until you have these answers:

|#|Question|Evidence|
|---|---|---|
|1|Did all unit tests pass?|The full-suite count from the project test command. A partial run is not an answer.|
|2|Did all E2E tests pass? (Only if the project has an E2E suite.)|The count from the project E2E runner. If the environment blocks a test, name the test and the cause.|
|3|Are new E2E cases necessary? (Only if the project has an E2E suite.)|Yes or no, with the reason. The answer is yes when the change reads or writes data through a path that unit tests mock. Show that the new case fails on `main` and passes on the PR.|

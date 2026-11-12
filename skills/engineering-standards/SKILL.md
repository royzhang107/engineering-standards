---
name: engineering-standards
description: Engineering and review standards. Use them when you write, review, or merge code. Use them for commit messages, PR text, and code comments. Use them when you plan a design change or investigate an incident.
---

# Engineering Standards

Apply these rules to every coding, review, and investigation task.
Repo rules (`CLAUDE.md`, `AGENTS.md`, `.claude/rules/`) outrank this skill. This skill outranks memory.

## 1. Hard rules

|Rule|Do this|
|---|---|
|Verify or ask|Each claim needs a source that you read in this session: `file:line`, log line, query result, or command output. If no tool can verify it, ask. Mark an unverified point "unverified".|
|No shared-state action without an explicit order|Push, PR, merge, approve, deploy, and image push to a shared tag need an explicit order in the current turn. Plan approval and earlier turns do not count.|
|No cloud action without an explicit order|Do not call cloud CLIs, SDKs, or MCP tools until the user asks in the current turn. Do not pick an account or profile by its name. Ask.|
|Pre-flight for destructive operations|Destructive operations are: delete or drop, IAM or network changes, scale-down, DNS or TLS changes, and `DELETE` without `WHERE`. Before each one, show the operation, assumptions, risks, and rollback. Wait for confirmation.|
|Secrets: names only|When you read `.env*`, compose env, container env, or a secret store, print variable names only. Do not print values or masked values. Compare secrets by hash.|
|Do the literal ask|Do not add scope, features, or "while I am here" changes. After you raise a concern, stop and wait.|

### What counts as a guess

|Guess|Correct action|
|---|---|
|"The code handles value X. So the external system sends X."|Code that handles a value does not prove that the value occurs. Find a real payload, or ask.|
|A UI option, menu, or operator workflow in an external system|Ask the user. The code does not show the screens.|
|"I read the helper. The caller surely wires it."|Read the caller.|
|"It probably runs X on startup."|Trace the full chain: compose, image, entrypoint, script.|
|"The deploy said Started. It is fine."|Read the logs. "Started" does not mean "healthy".|
|"My analysis says X." (live data exists)|Check the data first. A theory that contradicts live data is wrong.|
|"Probably", "likely", "should be"|Verify it, or ask.|

## 2. Design

### Correctness, completeness, consistency

Check these items before you propose or make a design change:

|Check|Question|
|---|---|
|Correctness|Does the design work in all states and transitions? Check concurrency, race conditions, and failure modes.|
|Completeness|Do all related code paths, data stores, and external systems get the same update?|
|Consistency|Does the change agree with the existing model? If the model changes, trace and update each dependent component.|

### Principles

|Principle|Rule|
|---|---|
|Simplicity first|Write the minimum code that solves the problem. Add no speculative feature, abstraction, configuration, or error handling for impossible cases.|
|Surgical changes|Touch only what the task needs. Match the existing style. Do not refactor working code. Remove only the orphans that your change made.|
|Root cause|Fix the root cause. Do not add a temporary workaround.|
|Goal-driven|Change each task into a check: "fix the bug" becomes "write a failing test, then make it pass".|
|Correctness over effort|Do not dismiss a correctness issue as "a larger refactor". Fix it.|
|Domain names first|Lock the domain name. Adapt infrastructure names to it. Do not rename domain entities to fix a collision.|

### Before an architectural action

1. State your assumptions about the current state. Verify them.
2. Name the worst realistic failure mode.
3. Look for a simpler way to get the same result.
4. List all options. Give each option one constraint that can eliminate it. Compare the survivors. Record the reason for the choice.
5. When the user says "go with X", check if X fits the current infrastructure, team, and timeline. Show the tradeoffs as questions.

Scale the depth to the risk:

|Input|Depth|
|---|---|
|Architecture decision|Full, with an options comparison|
|Production and destructive|3 to 5 questions|
|Production and additive|1 to 2 questions|
|Dev or staging|0 to 1 question|
|Known pattern, clear scope|No questions. Do the task.|

### Ask or decide

|Ask first|Decide alone|
|---|---|
|Architecture, destructive changes, breaking API contracts, unclear requirements|Bug fixes, test failures, details inside an approved plan, lint and format|

Doc-vs-code mismatch: a doc can describe something that the code does not have. Do not assume that the doc is wrong. It can be a planned feature. Ask: "Is this a doc error, or a feature that is not implemented yet?"

## 3. Git

|Rule|Detail|
|---|---|
|Branch before commit|Never commit to `main`.|
|No force push on shared branches|Never force-push `main`, a shared branch, or a branch that another person pushes to.|
|No `git add -f`|Ask first.|
|PR edits|Use `gh api repos/{owner}/{repo}/pulls/{number} -X PATCH` to update a PR body.|

### Judge the commit series

Do this before the first push and again before the merge. State the verdict in one line.

|Problem|Action|
|---|---|
|A later commit reverses the decision of an earlier commit|Squash or reorder|
|One commit bundles changes that you can revert independently|Split it|
|A commit has a different subject from the branch|Move it to a different branch|

Keep the history when each commit is a separate lever and the fixed mechanism is unverified. A series that you can bisect is better than a tidy series.

## 4. Writing

### Commit messages, PR text, code comments, error and log messages

Write in ASD-STE100 (Simplified Technical English):

|#|Rule|
|---|---|
|1|Keep each sentence short: 20 words or fewer. A descriptive sentence can have 25 words.|
|2|Write one idea in each sentence. Remove "so", "but", and "probably" joins.|
|3|Use the active voice. Name the actor: "The scheduler sends the email", not "The email is sent by the scheduler".|
|4|Use one term for one concept. Do not change to a synonym.|
|5|Use plain words ("must", "cannot", "necessary"). Do not write filler or clever phrasing.|
|6|Write each procedure step as one imperative sentence.|
|7|Do not copy the style of nearby text when that text breaks these rules.|

Before each commit and each PR body, make sure that each statement is still true for the final code. Fix or remove a stale sentence.

### Public surfaces: no local-only references

Commit messages, PR titles, PR bodies, and PR or issue comments must not refer to artifacts that reviewers cannot see. Examples: local ADRs, handoff notes, personal lessons, local scripts, `.http` files. Search the text before each commit and PR.

## 5. Investigation

### 6W1H for each incident step

Make a timeline with one row for each step. Fill each cell from a source (log line, audit row, query result). Write "unknown" when no source answers.

|Question|Meaning|
|---|---|
|When|Time, with time zone|
|Where|System or screen|
|Who|The actor, as an account ID or a role from the log|
|Whom|The receiver or target|
|What|The action|
|Why|The reason, only as written in the source|
|How|The method or path (for example, the API route)|

Keep Who and Whom separate. Do not assume that the actor of one step did the other steps. A guess about the physical world is not a "Why".

### Strikethrough is content

Struck-through text in a doc means "removed" or "replaced". Keep it when you extract text: convert `<s>`, `<del>`, `<strike>`, and `line-through` to `~~text~~`. Do not report struck text as a current requirement. If the tool cannot show the formatting, say that the read is incomplete.

## 6. Code review procedure

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
|Scope|Each changed line traces to the request. Flag drive-by refactors.|
|Tests|A test reproduces each fixed bug. The full suite passes.|
|Breaking changes|Flag API contract changes, migration risks, and downtime.|
|Infrastructure|Check idempotency and resource replacement risk.|
|Text|Commit and PR text follow section 4.|

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

## 7. Before "can merge"

Do not say that a PR can merge until you have these answers:

|#|Question|Evidence|
|---|---|---|
|1|Did all unit tests pass?|The full-suite count from the project test command. A partial run is not an answer.|
|2|Did all E2E tests pass? (Only if the project has an E2E suite.)|The count from the project E2E runner. If the environment blocks a test, name the test and the cause.|
|3|Are new E2E cases necessary? (Only if the project has an E2E suite.)|Yes or no, with the reason. The answer is yes when the change reads or writes data through a path that unit tests mock. Show that the new case fails on `main` and passes on the PR.|

## 8. Workflow

|Step|Rule|
|---|---|
|Plan|Plan before a task with 3 or more steps or changes to more than one file. If a fix fails twice, stop and plan again.|
|Subagents|Give each subagent one objective. Make it write its findings to a file.|
|Verify|Do not mark a task done until you prove that it works. Compare `main` with your change when it is relevant.|
|Elegance|For a change to 3 or more files, ask: "Is there a simpler design?" Skip this for an obvious one-file fix.|
|Bug reports|Read the logs, errors, and failing tests. Fix the bug. If the root cause stays unclear, give the top 2 hypotheses with evidence.|
|Stale docs|Notes and handoffs show the state at the time of writing. Verify the current code before you act on them.|

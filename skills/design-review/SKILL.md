---
name: design-review
description: Design checks and principles. Use them when you plan or make a design change, an architecture decision, or AWS infrastructure work.
---

# Design Review

Repo rules (`CLAUDE.md`, `AGENTS.md`, `.claude/rules/`) outrank this skill. This skill outranks memory.

## Correctness, completeness, consistency

Check these items before you propose or make a design change:

|Check|Question|
|---|---|
|Correctness|Does the design work in all states and transitions? Check concurrency, race conditions, and failure modes.|
|Completeness|Do all related code paths, data stores, and external systems get the same update?|
|Consistency|Does the change agree with the existing model? If the model changes, trace and update each dependent component.|

## Principles

|Principle|Rule|
|---|---|
|Simplicity first|Write the minimum code that solves the problem. Add no speculative feature, abstraction, configuration, or error handling for impossible cases.|
|Surgical changes|Touch only what the task needs. Match the existing style. Do not refactor working code. Remove only the orphans that your change made.|
|Root cause|Fix the root cause. Do not add a temporary workaround.|
|Goal-driven|Change each task into a check: "fix the bug" becomes "write a failing test, then make it pass".|
|Correctness over effort|Do not dismiss a correctness issue as "a larger refactor". Fix it.|
|Domain names first|Lock the domain name. Adapt infrastructure names to it. Do not rename domain entities to fix a collision.|

## Before an architectural action

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
|Known pattern, clear scope, not destructive|No questions. Do the task. A destructive operation still needs the "Pre-flight for destructive operations" rule of the `agent-guardrails` skill.|

## Ask or decide

|Ask first|Decide alone|
|---|---|
|Architecture, destructive changes, breaking API contracts, unclear requirements|Bug fixes, test failures, details inside an approved plan, lint and format|

Doc-vs-code mismatch: a doc can describe something that the code does not have. Do not assume that the doc is wrong. It can be a planned feature. Ask: "Is this a doc error, or a feature that is not implemented yet?"

## Two time sources

Many systems have two kinds of time. Do not mix them.

|Source|Examples|
|---|---|
|User input|A start or end time that a user enters, a deadline, a schedule|
|System time|The time when the server processes an action (`new Date()`)|

Before you compare a time with "now":

1. Name the source of each side: user input or system time.
2. State which clock "now" is. In event-processing code, "now" is the server processing time. It is not the time of the real event.
3. Check the delay between the event and the processing (webhook lag, retry, queue delay). A late process can push "now" past a user-input end time.
4. In tests, set the system time explicitly (for example, `jest.useFakeTimers().setSystemTime`). Put user-input times on both sides of it.

## AWS

|Rule|Detail|
|---|---|
|Infrastructure as code|Create infrastructure with AWS CDK or CloudFormation. Do not create it with direct CLI commands.|
|Well-Architected|Follow the AWS Well-Architected Framework.|
|Check the docs|If you are not sure about an API parameter, permission, limit, or error code, read the AWS docs. If you cannot confirm it, say so.|
|Resource names|Do not use em dashes in resource names or descriptions. Use hyphens.|
|Secrets|Do not call `secretsmanager get-secret-value` or `batch-get-secret-value`. Resolve secrets at runtime so that the value does not enter the agent context.|

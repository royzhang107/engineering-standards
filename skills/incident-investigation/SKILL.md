---
name: incident-investigation
description: 6W1H method and document-reading rules. Use them when you investigate an incident or write a report about it.
---

# Incident Investigation

Repo rules (`CLAUDE.md`, `AGENTS.md`, `.claude/rules/`) outrank this skill. This skill outranks memory.

## 6W1H for each incident step

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

## Strikethrough is content

Struck-through text in a doc means "removed" or "replaced". Keep it when you extract text: convert `<s>`, `<del>`, `<strike>`, and `line-through` to `~~text~~`. Do not report struck text as a current requirement. If the tool cannot show the formatting, say that the read is incomplete.

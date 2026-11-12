---
name: technical-writing
description: Writing rules for commit messages, PR text, code comments, error messages, and text for non-engineers. Use them when you write any of these texts.
---

# Technical Writing

Repo rules (`CLAUDE.md`, `AGENTS.md`, `.claude/rules/`) outrank this skill. This skill outranks memory.

## Commit messages, PR text, code comments, error and log messages

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

## Public surfaces: no local-only references

Commit messages, PR titles, PR bodies, and PR or issue comments must not refer to artifacts that reviewers cannot see. Examples: local ADRs, handoff notes, personal lessons, local scripts, `.http` files. Search the text before each commit and PR.

## Text for non-engineers

Write issue-tracker, chat, and email text for a non-engineer, unless the user names an engineering audience. Earlier technical comments on a ticket do not change this default. PR bodies and PR comments stay engineering text.

|Do not write|Write instead|
|---|---|
|PR numbers, commit SHAs, branch names|"fixed in dev", "deployed to staging"|
|Component, file, function, or field names|The screen and the action ("Settings > Profile")|
|The cause in code terms|What was wrong on the screen, in one sentence|
|Test evidence|What to check, and where|

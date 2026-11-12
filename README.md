# engineering-standards

A Claude Code plugin with one skill: `engineering-standards`. The skill holds shared engineering and review rules: verification, design checks, Git, writing, investigation, and code review.

## Install

### As a plugin

```text
/plugin marketplace add royzhang107/engineering-standards
/plugin install engineering-standards@engineering-standards
```

To get a new version, run `/plugin marketplace update engineering-standards`.

### As a plain skill

Copy `skills/engineering-standards/` to one of these locations:

|Scope|Path|
|---|---|
|All your projects|`~/.claude/skills/engineering-standards/`|
|One repo (shared with the team)|`<repo>/.claude/skills/engineering-standards/`|

Claude Code loads the skill when a task matches its description. To start it by hand, ask: "Review this PR with the engineering-standards skill."

## Recommended plugins

Add the marketplace once, then install the plugin:

```text
/plugin marketplace add <marketplace repo>
/plugin install <plugin>@<marketplace>
```

|Plugin|Marketplace repo|Use|
|---|---|---|
|`codex@openai-codex`|`openai/codex-plugin-cc`|Second-opinion review by Codex: `/codex:review`, `/codex:adversarial-review`|
|`agent-skills@addy-agent-skills`|`addyosmani/agent-skills`|Skills for code review, TDD, debugging, security, planning, Git workflow|
|`playwright@claude-plugins-official`|`anthropics/claude-plugins-official`|Browser tests and UI checks|
|`frontend-design@claude-plugins-official`|`anthropics/claude-plugins-official`|Frontend UI work|

## Recommended skills

Install with `npx skills add <repo> --skill <name>`.

|Skill|Repo|Use|
|---|---|---|
|`karpathy-guidelines`|`forrestchang/andrej-karpathy-skills`|Rules against common LLM coding mistakes: overcomplication, unrequested changes, hidden assumptions|
|`grill-me`|`mattpocock/skills`|The agent questions your plan before you build it|
|`grill-with-docs`|`mattpocock/skills`|The same, checked against the project docs|
|`codebase-design`|`mattpocock/skills`|Module interfaces, seams, testability|
|`domain-modeling`|`mattpocock/skills`|Domain terms and architecture decision records|
|`find-skills`|`vercel-labs/skills`|Finds a skill for a task|
|AWS skills (`aws-cdk`, `aws-serverless`, `aws-containers`, and others)|`aws/agent-toolkit-for-aws`|AWS service guidance. Install only the skills that you need.|

## Recommended MCP server

|Server|Repo|Use|
|---|---|---|
|`code-review-graph`|`tirth8205/code-review-graph`|Builds a code graph. Gives impact radius, affected flows, and review context for a diff.|

---
description: Agent skills for the software development life cycle (technical specs, test plans), for Claude Code and other agents.
generated:
  at: 2026-10-30T08:13:24Z
  by: claude-code/2.1.314
id: sdlc-plugin
links:
  - note: A plugin served from the dzango-agent-plugins marketplace
    rel: part-of
    to: ../README.md
sources:
  - id: agent-skills
    resource: https://agentskills.io
    title: Agent Skills specification
  - id: plugin
    resource: sdlc/.claude-plugin/plugin.json
    title: sdlc plugin manifest
  - id: skills-cli
    resource: https://github.com/vercel-labs/skills
    title: skills CLI
  - id: specs
    resource: sdlc/skills/specs/SKILL.md
    title: specs skill
  - id: tests
    resource: sdlc/skills/tests/SKILL.md
    title: tests skill
status: stable
tags:
  - agent-skills
  - claude-code
  - plugin
  - sdlc
title: sdlc plugin
type: Plugin
---

# sdlc plugin

Part of the [dzango-agent-plugins](../README.md) marketplace.

Agent skills for the software development life cycle (technical specs, test plans), for Claude Code and other agents.[^plugin]

## Skills

- `specs`: interviews the user to turn a messy request into a single technical spec for exactly one feature, bug fix or refactoring.[^specs]
  - The spec is AI-XF 0.5 markdown, a superset of OKF 0.2.
  - The skill first settles the codebase scope (a single repo, an app in a monorepo, or several repos) and the folder the specs live in.
- `tests`: turns a given spec (a single markdown file, e.g. from `specs`) into a test plan, without code.[^tests]
  - The tests are grouped by level, from Gherkin acceptance tests down to unit tests.
  - Each test is traced to a spec item.
  - The plan covers the same codebase scope as the spec.

Both skills publish their document as a draft in a pull request, and stop there. The pull request's review and merge approve it: on merge, the [okf-approve](https://github.com/dzangolab/okf-actions/tree/main/approve) GitHub Action, run by the repository that contains the documents, marks them `stable` and records who vouched for them, following the repository's rules.[^specs]

## Usage

Once the skills are installed, `specs` activates whenever you ask your agent to write, draft or refine a spec for a change.[^specs] In Claude Code, invoke it directly with `/sdlc:specs <your request>`.

`tests` activates when you ask for a test plan for a spec.[^tests] In Claude Code, invoke it directly with `/sdlc:tests <path to the spec>`.

## Install in Claude Code

This plugin is served from the [dzango-agent-plugins](../README.md) marketplace:

```
/plugin marketplace add dzangolab/dzango-agent-plugins
/plugin install sdlc@dzango
```

For quick local iteration without installing, launch Claude Code with the plugin loaded directly:

```
claude --plugin-dir /path/to/plugins/sdlc
```

## Install in other agents

The skills are plain [Agent Skills](https://agentskills.io) (a folder with a `SKILL.md`), so other agents that read that format, such as Codex, Gemini CLI, GitHub Copilot or Cursor, can use them too.[^agent-skills] Install them with the [`skills` CLI](https://github.com/vercel-labs/skills), which copies them to each agent's skills folder:[^skills-cli]

```
npx skills add dzangolab/dzango-agent-plugins --skill specs
npx skills add dzangolab/dzango-agent-plugins --skill tests
```

[^agent-skills]: Agent Skills specification, https://agentskills.io
[^plugin]: sdlc plugin manifest, `sdlc/.claude-plugin/plugin.json`
[^skills-cli]: skills CLI, https://github.com/vercel-labs/skills
[^specs]: specs skill, `sdlc/skills/specs/SKILL.md`
[^tests]: tests skill, `sdlc/skills/tests/SKILL.md`

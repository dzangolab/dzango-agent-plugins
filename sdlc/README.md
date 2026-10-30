---
description: Claude Code skills for the software development life cycle (technical specs, test plans).
generated:
  at: 2026-10-30T05:22:35Z
  by: claude-code/2.1.314
id: sdlc-plugin
links:
  - note: A plugin served from the dzango-agent-plugins marketplace
    rel: part-of
    to: ../README.md
sources:
  - id: plugin
    resource: sdlc/.claude-plugin/plugin.json
    title: sdlc plugin manifest
  - id: specs
    resource: sdlc/skills/specs/SKILL.md
    title: specs skill
  - id: tests
    resource: sdlc/skills/tests/SKILL.md
    title: tests skill
status: stable
tags:
  - claude-code
  - plugin
  - sdlc
title: sdlc plugin
type: Plugin
---

# sdlc plugin

Part of the [dzango-agent-plugins](../README.md) marketplace.

Claude Code skills for the software development life cycle (technical specs, test plans).[^plugin]

## Skills

- `specs`: interviews the user to turn a messy request into a single technical spec for exactly one feature, bug fix or refactoring.[^specs]
  - The spec is AI-XF 0.5 markdown, a superset of OKF 0.2.
  - The skill first settles the codebase scope (a single repo, an app in a monorepo, or several repos) and the folder the specs live in.
- `tests`: turns a given spec (a single markdown file, e.g. from `specs`) into a test plan, without code.[^tests]
  - The tests are grouped by level, from Gherkin acceptance tests down to unit tests.
  - Each test is traced to a spec item.
  - The plan covers the same codebase scope as the spec.

## Usage

Once the plugin is installed, `specs` activates whenever you ask Claude to write, draft or refine a spec for a change.[^specs] To invoke it directly, run `/sdlc:specs <your request>`.

`tests` activates when you ask for a test plan for a spec.[^tests] To invoke it directly, run `/sdlc:tests <path to the spec>`.

## Install

This plugin is served from the [dzango-agent-plugins](../README.md) marketplace:

```
/plugin marketplace add dzangolab/dzango-agent-plugins
/plugin install sdlc@dzango
```

For quick local iteration without installing, launch Claude Code with the plugin loaded directly:

```
claude --plugin-dir /path/to/plugins/sdlc
```

[^plugin]: sdlc plugin manifest, `sdlc/.claude-plugin/plugin.json`
[^specs]: specs skill, `sdlc/skills/specs/SKILL.md`
[^tests]: tests skill, `sdlc/skills/tests/SKILL.md`

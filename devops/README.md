---
description: Shared Claude Code skills for infrastructure-as-code projects (Pulumi, Ansible, AWS).
generated:
  at: 2026-10-30T05:22:35Z
  by: claude-code/2.1.314
id: devops-plugin
links:
  - note: A plugin served from the dzango-agent-plugins marketplace
    rel: part-of
    to: ../README.md
sources:
  - id: plugin
    resource: devops/.claude-plugin/plugin.json
    title: devops plugin manifest
  - id: readme-generator
    resource: devops/skills/readme-generator/SKILL.md
    title: readme-generator skill
status: stable
tags:
  - claude-code
  - infrastructure-as-code
  - plugin
title: devops plugin
type: Plugin
---

# devops plugin

Part of the [dzango-agent-plugins](../README.md) marketplace.

Claude Code skills shared across infrastructure-as-code projects (Pulumi, Ansible, AWS).[^plugin]

## Skills

- `readme-generator`: creates or updates a project's `README.md` so that it conforms to OKF v0.2 and to the repo's standard structure.[^readme-generator]

## Usage

Once the plugin is installed, `readme-generator` activates on its own in these cases:[^readme-generator]
- you ask Claude to create or update a project's `README.md`;
- a new Pulumi project or Ansible control directory is added;
- an existing `README.md` is missing frontmatter or has drifted from the standard structure.

You can also prompt it directly, e.g. "use readme-generator to update this project's README".

## Install

This plugin is served from the [dzango-agent-plugins](../README.md) marketplace:

```
/plugin marketplace add dzangolab/dzango-agent-plugins
/plugin install devops@dzango
```

For quick local iteration without installing, launch Claude Code with the plugin loaded directly:

```
claude --plugin-dir /path/to/plugins/devops
```

[^plugin]: devops plugin manifest, `devops/.claude-plugin/plugin.json`
[^readme-generator]: readme-generator skill, `devops/skills/readme-generator/SKILL.md`

# agent-plugins

dzangolab's marketplace repo hosting multiple Claude Code plugins, one per folder.

## Plugins

- [`devops`](devops/README.md) — shared skills for infrastructure-as-code projects (Pulumi, Ansible, AWS).

## Install

Add this repo as a marketplace once, then install whichever plugin(s) you need:

```
/plugin marketplace add dzangolab/agent-plugins
/plugin install devops@dzangolab-agent-plugins
```

## Adding a new plugin

1. Create a new folder at the repo root (e.g. `generate-c4-docs/`) with its own `.claude-plugin/plugin.json` and a `skills/` (and/or `agents/`, `hooks/`, `commands/`, `.mcp.json`) directory — same shape as `devops/`.
2. Add an entry for it to `.claude-plugin/marketplace.json` with `"source": "./generate-c4-docs"`.
3. `/plugin install <name>@dzangolab-agent-plugins` to pick it up.

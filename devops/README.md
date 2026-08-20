# devops plugin

Claude Code skills shared across infrastructure-as-code projects.

## Skills

- `readme-generator` — create or update a project's README.md to conform to OKF v0.2 and a repo's standard structure.

## Install

This plugin is served from the [`agent-plugins`](../README.md) marketplace repo one level up:

```
/plugin marketplace add dzangolab/agent-plugins
/plugin install devops@dzangolab-agent-plugins
```

For quick local iteration without installing, launch Claude Code with the plugin loaded directly:

```
claude --plugin-dir ~/projects/ai/plugins/devops
```

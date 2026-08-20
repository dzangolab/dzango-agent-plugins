# devops plugin

Claude Code skills shared across infrastructure-as-code projects.

## Skills

- `readme-generator` — create or update a project's README.md to conform to OKF v0.2 and a repo's standard structure.

## Install

This plugin is served from the [`plugins`](../README.md) marketplace repo one level up:

```
/plugin marketplace add ~/projects/ai/plugins        # or a git URL once pushed
/plugin install devops@plugins
```

For quick local iteration without installing, launch Claude Code with the plugin loaded directly:

```
claude --plugin-dir ~/projects/ai/plugins/devops
```

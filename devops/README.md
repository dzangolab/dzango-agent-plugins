# devops plugin

Claude Code skills shared across infrastructure-as-code projects.

## Skills

- `readme-generator` — create or update a project's README.md to conform to OKF v0.2 and a repo's standard structure.

## Usage

Once installed, `readme-generator` activates automatically whenever you ask Claude to create or update a project's `README.md`, or when it notices one is missing OKF v0.2 frontmatter or has drifted from the repo's standard structure — no explicit invocation needed. You can also prompt it directly, e.g. "use readme-generator to update this project's README".

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

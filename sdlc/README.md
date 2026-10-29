# sdlc plugin

Claude Code skills for the software development life cycle.

## Skills

- `specs` — interview the user to turn a messy request into a single technical spec (AI-XF 0.5 markdown, a superset of OKF v0.2) for exactly one feature, bug fix, or refactoring.

## Usage

Once installed, `specs` activates whenever you ask Claude to write, draft or refine a spec for a change. You can also invoke it directly with `/sdlc:specs <your request>`.

## Install

This plugin is served from the [`agent-plugins`](../README.md) marketplace repo one level up:

```
/plugin marketplace add dzangolab/agent-plugins
/plugin install sdlc@dzangolab-agent-plugins
```

For quick local iteration without installing, launch Claude Code with the plugin loaded directly:

```
claude --plugin-dir ~/projects/ai/plugins/sdlc
```

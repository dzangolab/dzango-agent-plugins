# sdlc plugin

Claude Code skills for the software development life cycle.

## Skills

- `specs` — interview the user to turn a messy request into a single technical spec (AI-XF 0.5 markdown, a superset of OKF v0.2) for exactly one feature, bug fix, or refactoring.
- `tests` — turn a given spec (a single markdown file, e.g. from `specs`) into a test plan, without code: meaningful tests grouped by level from Gherkin acceptance tests down to unit tests, each traced to the spec.

## Usage

Once installed, `specs` activates whenever you ask Claude to write, draft or refine a spec for a change; invoke it directly with `/sdlc:specs <your request>`.

`tests` activates when you ask for a test plan for a spec; invoke it directly with `/sdlc:tests <path to the spec>`.

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

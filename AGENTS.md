# dzango-agent-plugins

The `dzango` Claude Code marketplace: one plugin per top-level folder, listed in `.claude-plugin/marketplace.json`.

## Layout

- `.claude-plugin/marketplace.json`: the marketplace (`name: dzango`) and one entry per plugin, with a relative `source`.
- `<plugin>/.claude-plugin/plugin.json`: the plugin manifest. The entry name in `marketplace.json` and the manifest `name` must match.
- `<plugin>/skills/<skill>/SKILL.md`: one folder per skill, with its supporting files (e.g. `template.md`) beside it.
- `<plugin>/README.md`: the plugin's README.

## Conventions

- Names are permanent: never rename the marketplace or a plugin without a `renames` entry in `marketplace.json`. Change `displayName` instead.
- Bump the plugin's `version` in `plugin.json` on every change to it, or users keep the old copy.
- Every README is an [AI-XF](https://github.com/DavidROliverBA/ai-xf-format/blob/main/SPEC.md) concept:
  - frontmatter with `type`, a unique `id`, `generated`, `sources` and `status`;
  - every factual statement footnoted with a `sources[].id`;
  - each plugin README has a `part-of` link to `../README.md`, mirrored by a body link.
- Use placeholder paths in docs (`/path/to/plugins/<plugin>`), never paths from one machine.
- Keep `plugin.json` keys alphabetical after `name`.

## Checks

Run before every PR; each must end with `✔ Validation passed`:

```
claude plugin validate --strict .
claude plugin validate --strict <plugin>
```

The plugin-directory checks (licence, icon) are reported but don't fail the run.

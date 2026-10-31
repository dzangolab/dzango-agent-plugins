---
description: dzangolab's Claude Code marketplace, hosting one plugin per folder.
generated:
  at: 2026-10-31T16:50:18Z
  by: claude-code/2.1.316
id: dzango-agent-plugins
resource: https://github.com/dzangolab/dzango-agent-plugins
sources:
  - id: license
    resource: LICENSE
    title: MIT License
  - id: marketplace
    resource: .claude-plugin/marketplace.json
    title: Marketplace manifest
status: stable
tags:
  - claude-code
  - marketplace
title: dzango-agent-plugins
type: Marketplace
---

# dzango-agent-plugins

dzangolab's marketplace repo for the `dzango` marketplace. It hosts several Claude Code plugins, one per folder.[^marketplace]

## Plugins

- [`devops`](devops/README.md): shared skills for infrastructure-as-code projects (Pulumi, Ansible, AWS).[^marketplace]
- [`sdlc`](sdlc/README.md): skills for the software development life cycle (technical specs, test plans, implementation plans).[^marketplace]

## Install

Add this repo as a marketplace once, then install the plugins you need:

```
/plugin marketplace add dzangolab/dzango-agent-plugins
/plugin install devops@dzango
/plugin install sdlc@dzango
```

## Adding a new plugin

1. Create a folder at the repo root (e.g. `generate-c4-docs/`) with:
   - its own `.claude-plugin/plugin.json`;
   - a `skills/` directory, and/or `agents/`, `hooks/`, `commands/` or `.mcp.json`.

   This is the same shape as `devops/`.
2. Add a `README.md` to the folder, following the [AI-XF format](https://github.com/DavidROliverBA/ai-xf-format/blob/main/SPEC.md), like the existing plugin READMEs:
   - frontmatter with `type: Plugin`, a unique `id`, `generated`, `sources` and `status`;
   - a `part-of` link to this README, mirrored by a body link back to it.
3. Add an entry for the plugin to `.claude-plugin/marketplace.json` with `"source": "./generate-c4-docs"`, and list it under [Plugins](#plugins).
4. Run `/plugin install <name>@dzango` to pick it up.

## License

[MIT](LICENSE).[^license]

[^license]: MIT License, `LICENSE`
[^marketplace]: Marketplace manifest, `.claude-plugin/marketplace.json`

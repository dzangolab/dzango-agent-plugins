---
name: readme-generator
description: Create or update a project's README.md in this repo to conform to the OKF v0.2 spec and the repo's standard structure. Use whenever adding a new Pulumi project or Ansible control directory, or when an existing README.md is missing frontmatter or drifted from the standard structure.
---

# README generator

Every folder in this repo must have its own `README.md`, conformant with [OKF v0.2](https://github.com/GoogleCloudPlatform/knowledge-catalog/tree/main/okf): a `---`-delimited YAML frontmatter block with a non-empty `type` field, followed by a markdown body.

Reference implementations: `aws/ecr/README.md` and `aws/ssh-keypairs/README.md`. Read one before writing a new README — mirror its shape exactly rather than improvising.

## Frontmatter

```yaml
---
type: Pulumi Project           # or "Reference" for an index-only folder
title: <Project display name>
description: <one-sentence summary>
tags: [aws, pulumi, ...]
generated: { by: claude-code/claude-sonnet-5, at: <ISO 8601 datetime> }
---
```

## Body structure for a project README

1. `# <Title>` — matches frontmatter `title`.
2. `[← <Parent>](<relative path to the parent folder's README.md>)` — link back up the tree.
3. A one-paragraph summary of what the project provisions.
4. `# Stacks provisioned` — one `## <stack name>` subsection per Pulumi stack, each with:
   - The AWS account and region.
   - `### <Resource type>` subsection(s), one per kind of resource provisioned — a table of the actual provisioned resources when there's more than one, and a code block for any stack outputs.
   - A line stating the stack's actual `protect`/`retainOnDelete` config values and what they mean.
   - Any resource-specific caveats (e.g. import limitations).
5. `# Backend` — the shared Pulumi S3 backend and secrets provider this project logs into.
6. `# How to use` — a bullet list, in order: requirements, `cd <project> && npm install`, `direnv allow`, `pulumi stack select <stack>` (or `pulumi stack init ...` for a new one), how to change config and `pulumi up`, `pulumi destroy` to tear down. `direnv allow` is always the 2nd instruction, right after `npm install`.
7. `## Configuration settings` — a table (`Setting | Type | Default | Description`) of every stack config setting the project reads.

## Body structure for an index-only README

A folder with no Pulumi/Ansible resources of its own (e.g. `aws/`, `aws/swarm/`) skips "Stacks provisioned"/"Backend"/"How to use" and instead lists and links to its subfolders' READMEs, one `# <Subfolder title>` section per subfolder. See `aws/README.md` and `aws/swarm/README.md`.

## Folder names in section titles

Whenever a section title is derived from a folder name — index-only subfolder sections, or any other heading named after a folder — format it as title case with proper abbreviations fully capitalized (`AWS`, `SSH`, `S3`, `EBS`, `EIP`, `VPC`, `ECR`, `GitHub`) and no trailing `/`: e.g. `ssh-keypairs` → `# SSH Keypairs`, `swarm-leader` → `# Swarm Leader`, `aws` → `# AWS`. Never title-case a stack/environment/date identifier embedded in a title (e.g. `prod-20260803`) — keep those segments verbatim.

## Before writing

Read the project's `config.ts` and `Pulumi.<stack>.yaml` file(s) to get declared config (account/region, resource names, `protect`/`retainOnDelete`, config settings and their real defaults) — never guess or copy values from a sibling project's README.

Then, for "Stacks provisioned", prefer live data over static config: log into the shared backend (`pulumi login s3://pulumi-s3-backend-b63ba3d`, or the project's own backend if it has one), `pulumi stack select <stack>`, and run `pulumi stack output --json` and `pulumi config` to get actual current outputs/settings, plus the relevant `aws <service> describe-*` call(s) to pull real ARNs/URIs/timestamps/attributes for the resources that stack has provisioned. Document what's actually deployed, not just what the code declares.

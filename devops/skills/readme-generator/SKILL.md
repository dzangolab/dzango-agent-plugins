---
name: readme-generator
description: Create or update a project's README.md in this repo to conform to the OKF v0.2 spec and the repo's standard structure. Use whenever adding a new Pulumi project or Ansible control directory, or when an existing README.md is missing frontmatter or drifted from the standard structure.
---

# README generator

Every folder in this repo must have its own `README.md`, conformant with [OKF v0.2](https://github.com/GoogleCloudPlatform/knowledge-catalog/tree/main/okf): a `---`-delimited YAML frontmatter block with a non-empty `type` field, followed by a markdown body.

Resources live under one top-level folder per cloud provider (e.g. `aws/`, `azure/`, `gce/`) — read a couple of existing `README.md` files under the relevant provider folder before writing a new one, and mirror their shape exactly rather than improvising.

## Frontmatter

```yaml
---
type: Pulumi Project           # or "Reference" for an index-only folder
title: <Project display name>
description: <one-sentence summary>
tags: [<cloud provider, e.g. aws/azure/gcp>, pulumi, ...]
generated: { by: claude-code/claude-sonnet-5, at: <ISO 8601 datetime> }
---
```

## Body structure for a project README

1. `# <Title>` — matches frontmatter `title`.
2. `[← <Parent>](<relative path to the parent folder's README.md>)` — link back up the tree.
3. A one-paragraph summary of what the project provisions.
4. `# Stacks provisioned` — one `## <stack name>` subsection per Pulumi stack, each with:
   - The cloud account/project and region — whatever the provider calls it (AWS account, GCP project, Azure subscription).
   - `### <Resource type>` subsection(s), one per kind of resource declared in code — a table of the resources when there's more than one, and a code block for the stack's outputs (from `index.ts`'s `export =`, or `pulumi stack output --json` for real current values).
   - A line stating the stack's declared `protect`/`retainOnDelete` config values and what they mean.
   - Any resource-specific caveats (e.g. import limitations).
5. `# Backend` — the shared Pulumi backend (e.g. an S3 bucket, GCS bucket, or Azure Blob container) and secrets provider this project logs into.
6. `# How to use` — a bullet list, in order: requirements, `cd <project> && npm install`, `direnv allow`, `pulumi stack select <stack>` (or `pulumi stack init ...` for a new one), how to change config and `pulumi up`, `pulumi destroy` to tear down. `direnv allow` is always the 2nd instruction, right after `npm install`.
7. `## Configuration settings` — a table (`Setting | Type | Default | Description`) of every stack config setting the project reads.

## Body structure for an index-only README

A folder with no Pulumi/Ansible resources of its own (e.g. a cloud provider folder like `aws/` or `azure/`, or a grouping folder like `aws/swarm/`) skips "Stacks provisioned"/"Backend"/"How to use" and instead lists and links to its subfolders' READMEs, one `# <Subfolder title>` section per subfolder.

## Folder names in section titles

Whenever a section title is derived from a folder name — index-only subfolder sections, or any other heading named after a folder — format it as title case with proper abbreviations fully capitalized and no trailing `/`. Common ones across providers: `AWS`, `GCP`, `SSH`, `S3`, `EBS`, `EIP`, `VPC`, `ECR`, `IAM`, `GCE`, `GKE`, `GitHub` — extend as needed for whatever provider/service abbreviations appear in this repo's folder names. E.g. `ssh-keypairs` → `# SSH Keypairs`, `swarm-leader` → `# Swarm Leader`, `aws` → `# AWS`, `gce` → `# GCE`. Never title-case a stack/environment/date identifier embedded in a title (e.g. `prod-20260803`) — keep those segments verbatim.

## Before writing

Read the project's `config.ts`, `index.ts`, and `Pulumi.<stack>.yaml` file(s) to get declared config and resources (account/project/subscription, region, resource names, `protect`/`retainOnDelete`, config settings and their real defaults) — never guess or copy values from a sibling project's README.

For "Stacks provisioned" outputs, `pulumi stack ls` and `pulumi stack output` (against the shared backend, or the project's own backend if it has one) are fine to confirm what stacks exist and pull their real output values.

**Never call the cloud provider directly to check what resources actually exist** — no `aws`/`gcloud`/`az` CLI describe/get/show calls, and no cloud provider SDK calls (e.g. the AWS SDK) to inspect live resources. The README documents what the code declares (plus Pulumi's own stack outputs), not infrastructure state discovered by querying the provider.

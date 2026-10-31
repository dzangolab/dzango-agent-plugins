# ADR template

Use this when the project has no ADR format of its own: one file per decision, named `adr-<slug>.md`. Section guidance is in the HTML comments; remove the comments in the final document. The frontmatter follows the [AI-XF format section of the specs skill](../specs/SKILL.md#ai-xf-format), so `status: draft` means proposed, `stable` (set on merge) means accepted, and `deprecated` with a `superseded-by` link means superseded. An accepted ADR isn't rewritten: a change of decision gets a new ADR that supersedes it.

````markdown
---
description: <the decision, in one line>
generated:
  at: <ISO 8601 datetime with UTC offset>
  by: <agent>/<version>
id: adr-<slug>
links:
  - note: The plan that raised this decision
    rel: references
    to: <plan id>
status: draft
tags:
  - adr
title: "ADR: <decision in a few words>"
type: ADR
# Added on merge by okf-approve, never by hand:
# verified:
#   - at: <ISO 8601 datetime with UTC offset>
#     by: human:<GitHub login>
---

# ADR: <decision in a few words>

## Context

<!--
The problem and the forces at play: the spec's constraints, the code as it is, and the results of any feasibility
step. Link the plan that raised it. Facts only; don't argue for an option here.
-->

Raised by [<plan title>](/<plan-file>.md).

## Options

<!--
At least two workable options, each with its trade-offs and the coupling it creates.
-->

### <Option A>

- **For:** <advantages>
- **Against:** <disadvantages>
- **Coupling:** <dependencies between sub-systems it creates, or "None">

### <Option B>

- **For:** <advantages>
- **Against:** <disadvantages>
- **Coupling:** <dependencies between sub-systems it creates, or "None">

## Decision

<!--
The option chosen, who chose it, and why it beats the others, in a few sentences.
-->

## Consequences

<!--
What becomes easier and harder, what is now ruled out, what the team must now do or watch, and what would make this
decision worth revisiting.
-->
````

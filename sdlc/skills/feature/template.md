# Feature document template

Use these sections, in this order. Section guidance is in the HTML comments; remove the comments in the final document.

````markdown
---
description: <one-line summary of the intent, in plain language>
generated:
  at: <ISO 8601 datetime with UTC offset>
  by: <agent>/<version>
id: feature-<slug>
# Only when related documents exist; mirror each one with a body link
links:
  # For a sub-feature only: its parent feature, mirrored in section 2
  - note: <the stage of the parent's rollout this sub-feature delivers>
    rel: part-of
    to: <parent feature id>
  - note: <why they are related>
    rel: <relates-to | references | depends-on | supersedes>
    to: <target document id>
# Only when the document includes screenshots or other images; one entry per file
media:
  - hash: sha256:<hex>
    title: <caption>
    uri: <path in the bundle, or absolute URL>
sources:
  - id: <short-id>
    resource: <URL or path>
    title: <source title>
status: draft
tags:
  - <tag>
title: <Title>
type: Feature
# Added on merge by okf-approve, never by hand:
# verified:
#   - at: <ISO 8601 datetime with UTC offset>
#     by: human:<GitHub login>
---

# <Title>

## 1. Intent

<!-- One to three sentences, in plain language: what the feature lets people do, and why it matters. -->

## 2. Type

Feature

<!--
For a sub-feature only: the parent feature it is part of. The parent is done when all its sub-features are done.
-->

Part of [<parent feature title>](/<path to the parent feature>.md).

## 3. Context

<!--
Background a newcomer needs to understand why this matters, in the product's words:
- who asked for it, and why now;
- the problem it solves, and how people cope today;
- similar features in other products, when the user gave them (links).
Related documents: one plain link per `links` entry, e.g. "Related to [<title>](/<target-file>.md)."
-->

### Similar or related features in the app

<!--
The features the app already has that do something close to this one, or that this one works with
(not its own sub-features, which are listed in Rollout),
by the name people know them by, and what the user decided for each:
- Overlap: how this feature differs, or that the overlap is intended.
- Pattern to follow: what this feature does the same way, so people find what they expect.
- Works with: how the two features meet (e.g. the new one starts from, or adds to, the existing one).
Write "None found" when the user confirms there is nothing similar or related.
-->

| Existing feature | What it has in common | Decision |
| ---------------- | --------------------- | -------- |
| <name as people know it> | <overlap, shared pattern, or how they meet> | <distinct because… / intended overlap / follow its pattern for… / works with it by…> |

## 4. Personas

<!--
Everyone impacted, and how. Most impacts should be gains, but state losses honestly.
-->

| Persona | Impact | Gains | Losses |
| ------- | ------ | ----- | ------ |
| <name>  | Positive / Negative / Mixed | <concrete gain> | <concrete loss, or "None"> |

## 5. Outcomes

<!--
What is different for people, or for the business, once the feature is available. Each outcome is observable,
and measurable when the user gave a measure ("most" or "faster" are not measures).
-->

- <outcome>

## 6. Boundaries

<!--
What is explicitly not included, including related ideas the user chose to leave for another document or a later discussion.
-->

- <out-of-scope item>

## 7. Rollout

<!--
The stages in which the feature reaches people, in order. The feature is done when every stage is done.
Single stage: describe it directly, without sub-sections.
Several stages: one sub-section per stage. For each stage:
- what it delivers: part of this document's acceptance criteria, or a sub-feature, linked to its own feature document;
- who gets it: everyone, a group of people (e.g. invited customers, one team, the staff), in product terms;
- when it is done: a condition anyone can check, to move on to the next stage.
A sub-feature not written yet is named without a link; the link is added once its document exists.
-->

### Stage 1: <name>

- **Delivers:** <what people can do after this stage, or "The sub-feature [<title>](/<path to the sub-feature>.md)">
- **Who gets it:** <everyone, or which group of people>
- **Done when:** <condition anyone can check>

### Stage 2: <name>

- **Delivers:** <what people can do after this stage, or a sub-feature>
- **Who gets it:** <everyone, or which group of people>
- **Done when:** <condition anyone can check>

## 8. Acceptance criteria

<!--
One group per need, each for a persona from section 4, in the order people meet them.
Each criterion describes what a person does and sees, in Given / When / Then form, so that anyone can check it
by using the product. Include the unusual situations the user confirmed: nothing to show yet, a mistake,
missing permission, a limit reached.
Single stage: the groups directly, without stage sub-sections.
Several stages: one sub-section per stage of section 7, with the groups that stage delivers. A stage delivered
by a sub-feature only links to it: its acceptance criteria are in the sub-feature's document.
-->

### Stage 1: <name>

#### AC-1: <short name>

As a <persona>, I want <capability>, so that <benefit>.

- **Given** <situation>, **when** <action>, **then** <what the person sees or gets>.

### Stage 2: <name>

See [<sub-feature title>](/<path to the sub-feature>.md).

## 9. Screenshots, mockups and prototypes

<!--
The visuals the user supplied, each with a caption saying what it shows and why it matters.
- Screenshots stored in the bundle are shown in the document, and listed in `media`.
- Screenshots hosted elsewhere are linked, and listed in `media` with their URL.
- Mockups and prototypes are linked (e.g. a design file or a clickable prototype), and listed in `sources`.
They illustrate the intent: the acceptance criteria, not the visuals, say what the feature must do.
Write "None" when the user confirms there are none.
-->

![<caption>](<path in the bundle>)

*<caption: what it shows and why it matters>*

- [<screenshot title>](<URL>): <what it shows>
- [<mockup or prototype title>](<URL>): <what it shows>[^short-id]

<!-- Footnotes: one per cited source, label = sources[].id -->

[^short-id]: <source title>, <URL or path>
````

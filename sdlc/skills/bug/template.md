# Bug report template

Start with the header line under the title, then use these sections, in this order. Section guidance is in the HTML comments; remove the comments in the final document.

````markdown
---
description: <one-line summary of what goes wrong, in plain language>
generated:
  at: <ISO 8601 datetime with UTC offset>
  by: <agent>/<version>
id: bug-<slug>
# Only when related documents exist; mirror each one with a body link
links:
  - note: <why they are related, e.g. the feature whose behaviour the bug breaks>
    rel: <references | relates-to>
    to: <target document id>
# Only when the document includes screenshots, screen captures or other images; one entry per file
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
title: <What goes wrong, in people's words>
type: Bug report
# Added on merge by okf-approve, never by hand:
# verified:
#   - at: <ISO 8601 datetime with UTC offset>
#     by: human:<GitHub login>
---

# <What goes wrong, in people's words>

**Bug report**

## 1. Intent

<!-- One to three sentences, in plain language: what people get once the bug is fixed, and why it matters. -->

## 2. Context

<!--
In the product's words:
- who reported it, when, and how (support ticket, message, seen by the team);
- where in the product it happens;
- since when, and whether it used to work;
- how people cope today (a workaround, or "None").
Related documents: one plain link per `links` entry, e.g. "Breaks [<feature title>](/<target-file>.md)."
-->

## 3. Personas

<!--
Everyone affected, and what the bug costs them.
-->

| Persona | How many | What it costs them |
| ------- | -------- | ------------------ |
| <name>  | <everyone, a group, a number> | <concrete loss: time, money, data, trust> |

## 4. Steps to reproduce

<!--
Numbered, from a starting point anyone can reach (who is signed in, with what), down to the step where the bug shows.
When it can't be reproduced at will, say so, and give what is known instead: when it was seen, how often, by whom,
and what they were doing.
-->

**Starting point:** <who is signed in, with what>

1. <step>
2. <step>

## 5. Expected and actual

<!--
For each step where they differ: what people should see, and what they see instead.
The expected behaviour is the user's decision, or a link to the document that states it.
-->

| Step | Expected | Actual |
| ---- | -------- | ------ |
| <n>  | <what people should see> | <what they see instead> |

## 6. Where it happens

<!--
In the product's words: the apps, devices, browsers, languages, accounts or roles where it happens,
and where it doesn't. "Not checked" for what nobody looked at.
-->

- **Happens:** <where>
- **Doesn't happen:** <where, or "Not checked">

## 7. Frequency and impact

<!--
How often it happens (every time, once a day, 1 in 10 tries), how many people it reaches, and how bad it is for them.
Concrete figures when the user has them ("3 support tickets a week").
-->

- **Frequency:** <how often>
- **Impact:** <how bad, and for how many>

## 8. Screenshots and recordings

<!--
The visuals the user supplied, each with a caption saying what it shows.
- Files stored in the bundle are shown in the document, and listed in `media`, after the privacy check.
- Files hosted elsewhere are linked, and listed in `media` with their URL.
Write "None" when the user confirms there are none.
-->

![<caption>](<path in the bundle>)

*<caption: what it shows>*

- [<recording or ticket title>](<URL>): <what it shows>[^short-id]

## 9. Open questions

<!--
The questions the user chose to defer that no single section can answer yet.
A question tied to one section stays in that section, as `> **Open question:** …`.
Write "None" when there are none.
-->

- **<question>?** <why it is deferred, and when or how it will be decided>

<!-- Footnotes: one per cited source, label = sources[].id -->

[^short-id]: <source title>, <URL or path>
````

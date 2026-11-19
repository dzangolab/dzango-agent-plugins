---
name: bug
description: Interview the user to turn a vague bug report into a single bug report document - what goes wrong for exactly one bug, from the point of view of the people who use the product (AI-XF 0.5 markdown, a superset of OKF 0.2) - written in a non-technical product manager's voice, with no technical assumption, with steps to reproduce, expected and actual behaviour, who is affected and how badly, and the user's screenshots and recordings. The document is drafted live as the interview goes, so the user can follow it. Use when the user asks to write, draft, create, or refine a bug report, or invokes the bug skill directly (e.g. /sdlc:bug in Claude Code). Not for features or refactorings.
---

# Bug

Produce one bug report, in markdown conforming to AI-XF 0.5 (see [AI-XF format](#ai-xf-format)), that says what goes wrong for one bug, from the point of view of the people who use the product: what they do, what they expect, what they get instead, and what it costs them. It is the input of the technical spec written next with the [specs skill](../specs/SKILL.md), which finds the cause and the fix.

The user's report will be vague: "it doesn't work", a screenshot, a support ticket. Your first job is to **ask questions and clarify what happens**, not to write. Never assume: every fact, choice and inference that ends up in the document must be validated by the user.

## Rules

- **Bugs only.** A bug report describes one bug: the product doesn't do what it is meant to do.
  - A request for something the product was never meant to do is a feature: offer the [feature skill](../feature/SKILL.md) instead. When it isn't clear whether the behaviour is intended, ask; the answer often lies in an existing feature document.
  - A refactoring changes nothing people can see, so it has no bug report: offer the [specs skill](../specs/SKILL.md) instead.
  - When the report mixes several bugs, or a bug with a feature, the main bug gets this document and the rest are side issues: ask, for each, whether to write a separate document (after this one) or park it for a later discussion.
- **One bug per document.** Several symptoms with one cause are one bug; the same symptom in two unrelated places may be two. When unsure, ask; don't guess.
- The rules of the [feature skill](../feature/SKILL.md#rules) apply as they are: **suggest, never add**; **validate, don't assume**; **no technical content**; **write as a non-technical product manager**. In particular, the document names no suspected cause: what is wrong in the code is the spec's to find. A cause the user volunteers is noted for the spec in your final reply, not in the document.
- **Reproducible, or saying why not.** The steps to reproduce must let anyone see the bug by using the product. When the bug can't be reproduced at will, say so, and record what is known: when it was seen, how often, by whom, and what they were doing.
- **Complete enough for the spec.** The specs skill must be able to write the fix's spec without going back to the user with a product question: what people should see instead, for every situation the bug touches, is answered here. A product question the user chooses to defer stays visible as an open question, never as a silent gap.

## Screenshots and recordings

Ask the user for screenshots, screen recordings and links to support tickets or messages that show the bug. Handle them as the [feature skill](../feature/SKILL.md#screenshots-mockups-and-prototypes) does, privacy check included, with section 8 in place of its screenshots section: bug reports often show people's data, so the privacy check matters here most. Write "None" in section 8 only when the user confirms there are none.

## Live draft

Draft the document as the interview goes, as the [feature skill](../feature/SKILL.md#live-draft) does: create it once the bug is framed, with the `**Bug report**` line under the title and every other section as `> **Pending:** not discussed yet.`, and update it after each confirmed answer.

## Document location

Settle the folder as the [feature skill](../feature/SKILL.md#document-location) does, proposing the folder that holds the documents about bugs (e.g. `bugfixes/`) within a bundle. The document goes in the agreed folder as `bug-<slug>.md`, unless the folder already follows another naming convention. Check that no other document in the bundle already has that `id`. The technical spec written from it with the specs skill goes next to it as `<id>-spec.md`, and links to it as `specifies`.

## AI-XF format

The document is an [AI-XF 0.5](https://github.com/DavidROliverBA/ai-xf-format/blob/main/SPEC.md) concept, conforming at **Level 2 (AI-XF Full)**, with the same frontmatter, bundle files, timestamps, actors, links, footnotes, tombstones and validation rules as the specs skill: see the [AI-XF format section of the specs skill](../specs/SKILL.md#ai-xf-format), and [template.md](template.md) for the exact layout. The differences:

| Key | Value |
| --- | --- |
| `id` | `bug-<slug>`, same as the filename stem; unique in the bundle, never changed once assigned. |
| `links` | Typed edges to related documents in the bundle: e.g. `references` the feature document whose behaviour the bug breaks, `relates-to` a separate bug report split off from this one. |
| `media` | One entry per screenshot, screen capture or other image in the document: `uri` (required: bundle path or URL), `hash` (`sha256:<hex>`, for files in the bundle) and `title` (its caption). |
| `sources` | Support tickets, messages, recordings, logs given by the user, user documentation. |
| `type` | Always `Bug report`, shown as `**Bug report**` on the line under the title. The document always describes a bug, so it has no subject type. |

## Process

### 1. Frame the bug

1. Read the user's report and any linked material (tickets, messages, screenshots, recordings).
2. Settle the [document location](#document-location), and read the existing documents there (their `index.md` first when there is one): an existing bug report for the same bug, or the feature document that says what should happen.
3. List every distinct subject you see in the input. For each, note whether it is a bug, a feature or a refactoring.
4. Propose to the user:
   - a title, saying what goes wrong in people's words (e.g. "The account menu disappears after signing in again");
   - the main bug and a one-sentence draft of the intent: what people should get once it is fixed;
   - the side issues you identified, asking for each: separate document or later discussion.
5. Do not continue until the user confirms the bug and its intent.
6. Confirm the filename with the user, in the agreed [document location](#document-location), and [create the document](#live-draft) right away.

### 2. Interview

Work through the sections in order (see [template.md](template.md)), as the [feature skill's interview](../feature/SKILL.md#2-interview) does: one question at a time by preference, never more than 3; offer your proposed answer; hunt down vague words; turn technical answers into what people do and see.

- Cover: who reported it and when; where in the product it happens; since when, and whether it used to work; who is affected and how many; what it costs them; how they cope today.
- Steps to reproduce: numbered, from a starting point anyone can reach (who is signed in, with what), down to the step where the bug shows. Propose them and ask the user to confirm or correct them; ask whether they have reproduced it themselves.
- Expected and actual: what people should see, and what they see instead, for each step where they differ. When the expected behaviour isn't written anywhere, ask the user to decide it; don't infer it from what seems sensible.
- Where it happens: the apps, devices, browsers, languages, accounts or roles where it does and doesn't happen, in the product's words.
- Unusual situations the bug also touches: propose them with the behaviour expected, and ask the user to confirm.

### 3. Finish the draft, and review with the user

Follow the [feature skill](../feature/SKILL.md#3-finish-the-draft) for both steps, reading "bug" for "feature", with section 9 for the open questions no single section can answer. Publish with a commit such as `docs(bugfixes): add <document-id>`, and give the specs skill as the next step (e.g. `/sdlc:specs <path to the document>` in Claude Code).

## Quality checklist

Before presenting the draft, check that:

- [ ] Exactly one bug; no feature and no refactoring.
- [ ] The intent fits in one to three sentences and says what people get once the bug is fixed.
- [ ] Every affected persona is listed with what the bug costs them.
- [ ] The steps to reproduce let anyone see the bug by using the product, or the document says why it can't be reproduced at will and what is known.
- [ ] Expected and actual behaviour are stated for every step where they differ; the expected behaviour was decided by the user, not inferred.
- [ ] Where it happens and where it doesn't are stated in the product's words.
- [ ] Frequency and impact are concrete ("every time", "3 support tickets a week"), not "sometimes" or "a lot".
- [ ] Screenshots and recordings: every file is in the bundle, shown in the document and listed in `media` with its hash, after the privacy check; every hosted one is linked; or "None" is confirmed by the user.
- [ ] No suspected cause, technology, component or code; any technical point is something people can see, confirmed by the user.
- [ ] Complete for the spec: any deferred product question is an open question, inline in its section or in section 9.
- [ ] No `> **Pending:**` marker is left, and nothing was added that the user didn't confirm.
- [ ] The document is `status: draft`, in the location the user confirmed.
- [ ] AI-XF Level 2: same checks as the feature skill, with `type: Bug report`.

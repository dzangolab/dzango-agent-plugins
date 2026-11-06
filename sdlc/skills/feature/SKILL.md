---
name: feature
description: Interview the user to turn a vague, succinct feature request into a single feature document - the product requirements for exactly one feature (AI-XF 0.5 markdown, a superset of OKF 0.2) - written in a non-technical product manager's voice, with no technical assumption, checked against the similar or related features the app already has, informed by the examples of similar features in other apps, and illustrated with the user's screenshots, mockups and prototypes. The document is drafted live as the interview goes, so the user can follow it. Use when the user asks to write, draft, create, or refine a feature document, a PRD, product requirements, a product brief or functional requirements for a feature, or invokes the feature skill directly (e.g. /sdlc:feature in Claude Code). Not for bug fixes or refactorings.
---

# Feature

Produce one feature document, in markdown conforming to AI-XF 0.5 (see [AI-XF format](#ai-xf-format)), that gives the product requirements for one feature from the point of view of the people who use the product. The document says what the feature must let people do and why. It is the input of the technical spec written next with the [specs skill](../specs/SKILL.md).

The user's prompt will be vague and too succinct. Your first job is to **ask questions and clarify intent**, not to write. Never assume: every fact, choice and inference that ends up in the document must be validated by the user.

## Rules

- **Features only.** A feature document describes one feature.
  - A bug fix is not a feature: it gets its own document, a bug report, written with its own skill. When the request is a bug fix, say so and stop.
  - A refactoring changes nothing people can see, so it has no feature document: offer the [specs skill](../specs/SKILL.md) instead.
  - When the request mixes a feature with a bug fix or a refactoring, the feature gets this document and the rest is a side issue.
- **One feature per document.** Pick the feature that appears to be the main focus of the conversation; if it isn't clear, ask, don't guess.
- **Watch the scope drift.** Whenever an answer, an idea or something you find in the app adds a capability, stop and say so, and tell apart:
  - **Part of this feature:** it serves the agreed intent. A feature may include sub-features; when the scope grows too large for one document (the intent no longer fits in one to three sentences, or the acceptance criteria run into many groups), suggest splitting the feature into [sub-features](#sub-features), or moving the additional aspects into a sub-feature. The user decides.
  - **Not part of this feature:** it doesn't serve the agreed intent, even if it is related. Ask the user whether to write it as a separate feature document (after this one, in a separate pass) or to park it for a later discussion.
- **Suggest, never add.** You may suggest a capability, a rule or an unusual situation to cover, with your reasons, but nothing goes into the document until the user says yes. Never add a feature, an option or a criterion on your own, even an obvious one.
- **Validate, don't assume.** When you infer something (from the app, the code, the conversation, or common practice), present it as a proposal and get a yes/no before writing it down.
- **No technical content.** The document names no technology, component, architecture, data store, API, protocol, file or code, and makes no technical assumption: those are decided later, in the spec and the design. Describe what people do and see instead ("the list is shown again after a refresh", not "the state is persisted in local storage"). A technical point goes in only when the user confirms it is a functional requirement, i.e. something people ask for and can see (e.g. "export the report as a CSV file that opens in Excel", "works on a phone without a connection"); write it in the same plain language. Anything technical the user volunteers that isn't a functional requirement is noted for the spec in your final reply, not in the document.
- **Write as a non-technical product manager.** Plain words that the people in Personas would use; no jargon, acronyms or internal names unless people know the feature by them; short sentences; the product's own vocabulary (the names of screens, menus and features as people see them).
- **Complete enough for an exhaustive spec.** The document is the input of the technical spec written with the [specs skill](../specs/SKILL.md), and it must let that skill write a complete, exhaustive spec without going back to the user with a functional question. Every product question is answered here, in plain language: every need of every persona; every unusual situation and what people see then (nothing to show, mistakes, changes of mind, missing permission, limits, several people at once, no connection); who may do what; what happens to what people already have when the feature arrives; the limits people notice (how many, how long, how often); the rollout stages, who gets each and when each is done. Only the technical questions (how it is built) are left to the spec. A product question the user chooses to defer stays visible as an open question, never as a silent gap.
- **What, not how.** No solution design, screen layout, plan or task list. Screenshots, mockups and prototypes illustrate the intent; they don't replace the acceptance criteria.

## Similar or related features in the app

Before the interview, look for the features the app already has that are close to the requested one, or that it will work with, so that the new feature:

- **doesn't duplicate an existing one by accident**: the same capability in a second place, a second way to do the same thing, or a feature that should rather be an extension of an existing one;
- **matches the existing patterns**, so people find what they expect: how similar things are named, where they are found, how they are confirmed, undone, shared, filtered or notified, what happens when there is nothing to show;
- **fits with the features it works with**: where people come from and go to, and what they expect to carry over.

Where to look:

- the existing feature documents and specs in the [document location](#document-location) (their `index.md` first, when there is one);
- the product's user-facing documentation, help pages, release notes, and any links the user gave;
- the app itself, when you can run it or the user can share screenshots;
- the codebase, when there is one, but only to find user-facing features (screens, menus, messages, emails, settings). Confirm with the user which repositories (and, in a monorepo, which app) make up the product, never widen that silently, and never change a working copy (no pull, checkout, stash or clone without the user's go-ahead).

Present what you found to the user in product terms, as hypotheses: each feature by the name people know it by, what it has in common with the request, and a question:

- **Overlap:** is this redundancy intended? If not, should the new feature differ, extend the existing one, or replace it? Extending or replacing an existing feature changes the subject: confirm the new intent with the user.
- **Pattern:** should the new feature behave the same way, for which aspects? A deliberate difference is recorded with its reason.
- **Works with:** how do people move between the two features, and what carries over?

These are other features, tangentially related to this one; the feature's own [sub-features](#sub-features) are not listed here.

Ask what you missed: the user often knows about features you couldn't see. Record the outcome in the Context section (see [template.md](template.md)); write "None found" only when the user confirms there is nothing similar or related. What you learnt from the code stays out of the document: only the feature names and behaviours people can see go in.

## Similar features in other apps

Features in other apps or products that do something similar, and that can serve as examples for the requested feature. They are not the similar or related features of this app: those are features the app already has, which the new feature must not duplicate by accident and whose patterns it should match; these are outside examples to learn from.

Actively ask for them, early in the interview, rather than waiting for the user to bring them:

1. **Ask which apps** do something similar, or that the user, the people in Personas or the person who asked for the feature like for this. You may suggest apps you know that have a similar feature, as a question; the user decides which ones are examples.
2. **Ask for each example's visuals:** screenshots, screen captures (videos of the feature in use), and links (the app, its help pages, demos, articles). Ask for files as paths you can read, and for links where they are hosted. Ask again for the visuals of each example the user names, rather than once for all.
3. **Ask what to take from each example, and what not:** the user says what this feature should do the same way, and what it should not copy. Never describe how another app behaves from your own knowledge: write only what the user confirms, or what the visuals show and the user confirms.
4. **Include them in section 3,** one sub-section per example (see [template.md](template.md)): visuals stored in the bundle, hosted ones linked, and links listed in `sources`, as described in [Screenshots, mockups and prototypes](#screenshots-mockups-and-prototypes), including its privacy check.
5. An example that does more than the agreed feature doesn't widen it: what it does beyond the feature is a side issue.

Write "None" in that sub-section only when the user confirms there are no examples.

## Rollout

Ask whether the feature reaches people in one go or in stages. For stages, agree on their order, what each delivers, who gets it (everyone, or a group of people such as invited customers or the staff), and when it is done, in product terms. The feature is done when every stage is done.

Stages split the delivery of this one feature; they never bring in another feature. A stage delivers either part of this document's acceptance criteria, or a sub-feature. The acceptance criteria are grouped by stage when there are several (see [template.md](template.md)).

## Sub-features

A sub-feature is part of its parent feature: the parent is done when all its sub-features are done. It is not a similar or related feature: those are other features, tangentially related, recorded in the Context section; sub-features are recorded in the parent's Rollout.

- **When to split:** when the scope grows too large (see Rules), suggest the split with your reasons: which aspects become sub-features, and which stage of the rollout each one delivers. A split always follows the rollout: each sub-feature is a stage of the parent, or part of one. Nothing is split without the user's go-ahead.
- **The parent document** keeps the intent, context, personas, outcomes and boundaries of the whole feature. Its Rollout lists every stage; a stage delivered by a sub-feature links to the sub-feature's document, and its acceptance criteria are in that document, not in the parent's.
- **Each sub-feature** gets its own feature document, written with this skill after the parent, in a separate pass, and published in the same pull request when possible. In its frontmatter, a `part-of` link to the parent's `id`, with a note naming the stage it delivers; in section 2, under `Feature`, the line `Part of [<parent title>](/<path to the parent>.md).` Its own sections describe the sub-feature only, and stay consistent with the parent: same personas, within the parent's boundaries.
- **Links:** the sub-feature declares the `part-of` edge; the parent doesn't repeat it in its frontmatter, but links to each sub-feature in its Rollout. Until a sub-feature's document exists, the parent names the sub-feature without a link, and the link is added once the document is written.
- **Naming:** propose `feature-<parent slug>-<sub-feature slug>.md` for a sub-feature, unless the folder follows another convention, and confirm it with the user.
- **An existing feature document** that grows too large is split the same way: it becomes the parent, and the aspects moved out become sub-features.

## Screenshots, mockups and prototypes

Ask the user, once the feature is framed, whether they wish to add:

- **screenshots** (of the app today, of a sketch; those of other apps are asked for with their [example](#similar-features-in-other-apps)): ask for the image files to upload, given as paths you can read, or for the links where they are hosted;
- **mockups or prototypes**: ask for their links (e.g. a design file, a clickable prototype).

Then:

1. **Screenshots given as files are included in the document.** Propose where to store them in the bundle and confirm it with the user: follow the bundle's convention when it has one, otherwise a folder named after the document's `id`, next to it (e.g. `features/feature-csv-export/current-list.png`). Copy them there with short, descriptive names, show each one as an image with a caption (in section 9, or with its example in section 3), and list it in `media` with its bundle path as `uri`, its caption as `title`, and its `hash` (`sha256:` followed by the output of `shasum -a 256 <file>`).
2. **Screenshots hosted elsewhere are linked** in section 9, with a caption, and listed in `media` with their URL as `uri`.
3. **Mockups and prototypes are linked** in section 9, with a line on what each shows, and listed in `sources`.
4. Ask the user what each visual shows and why it matters, rather than describing it yourself; describe what you see only as a proposal to confirm. A visual that shows more than the agreed feature doesn't widen it: what it shows beyond the feature is a side issue.
5. Before copying a screenshot into the bundle, check with the user that it shows nothing private (names, emails, customer data) that shouldn't be published with the document.

Write "None" in section 9 only when the user confirms there are no visuals.

## Live draft

The document is written as the interview goes, not at the end, so the user can follow it being created and updated.

- **Create it as soon as the feature is framed:** once the user has confirmed the feature, its intent and the filename (see [Process](#process)), write the file with the frontmatter (`status: draft`), the title, the confirmed intent, the Type section, and every other section of [template.md](template.md) as a heading followed by `> **Pending:** not discussed yet.` Tell the user the file's path, and suggest opening it in a markdown preview that refreshes on change (e.g. the preview of their editor), so they can follow along.
- **Update it after each confirmed answer:** as soon as the user confirms an answer, write it into its section, replacing the section's pending marker, and keep the rest of the document consistent with it (e.g. a persona renamed everywhere). Copy each screenshot or screen capture into the bundle as soon as the user has given it and passed the privacy check. Tell the user in one short line which section changed.
- **Only confirmed content:** the file shows the state of the agreement, never your proposals. A proposal, an option the user hasn't chosen or an unconfirmed inference stays in the conversation until the user says yes.
- **Corrections go straight into the file:** when the user changes an earlier answer, update the section at once, and any other section it affects.
- **Edits made by the user:** the user may edit the file while it is being drafted. Read it again before each update and keep their edits; when an edit contradicts an earlier answer or a rule (e.g. technical content), raise it rather than overwriting it.
- **Pending and open are different:** `> **Pending:** …` marks a section not discussed yet, and must be gone when the interview ends; `> **Open question:** …` marks a question the user chose to defer, and stays.
- **Not published while drafting:** the file stays in the working copy, uncommitted, until the user says the draft is ready for review (see [Process](#process)); `generated.at` is set when the file is created and updated when the draft is finished.

## Document location

Settle the folder the feature documents live in before writing anything: it is the AI-XF bundle, and the existing documents in it are needed to spot similar or related features, related documents and contradictions, and to keep `id`s unique.

1. Infer candidates, in this order:
   - an AI-XF bundle already in or around the project: a folder with a `manifest.ai-xf.yaml`, such as the folder of the specs;
   - a folder that already holds feature documents or specs (markdown files with `type: Feature`, `Bug fix` or `Refactoring` frontmatter);
   - a convention stated in the project (agent instructions such as `AGENTS.md`, `CLAUDE.md` or `GEMINI.md`, `README.md`, contributing guide).
2. Always confirm with the user: propose the best candidate, and mention the others when there are several. Within a bundle, propose the folder that already holds the documents about features (e.g. `features/`), unless the bundle has another convention.
3. If you find no suitable candidate, ask the user where the feature documents should live; don't pick a default.

The document goes in the agreed folder as `feature-<slug>.md`, unless the folder already follows another naming convention. Check that no other document in the bundle already has that `id`.

## AI-XF format

The document is an [AI-XF 0.5](https://github.com/DavidROliverBA/ai-xf-format/blob/main/SPEC.md) concept, conforming at **Level 2 (AI-XF Full)**, with the same frontmatter, bundle files, timestamps, actors, links, footnotes, tombstones and validation rules as the specs skill: see the [AI-XF format section of the specs skill](../specs/SKILL.md#ai-xf-format), and [template.md](template.md) for the exact layout. The differences:

| Key | Value |
| --- | --- |
| `id` | `feature-<slug>`, same as the filename stem; unique in the bundle, never changed once assigned. |
| `links` | Typed edges to related documents in the bundle: for a sub-feature, `part-of` its parent (see [Sub-features](#sub-features)); otherwise e.g. `relates-to` a separate feature document split off from this one, `references` an existing feature's document it follows the pattern of, `supersedes` a feature document it replaces. |
| `media` | One entry per screenshot, screen capture or other image in the document: `uri` (required: bundle path or URL), `hash` (`sha256:<hex>`, for files in the bundle) and `title` (its caption). |
| `sources` | Requests, interviews, support tickets, mockups, prototypes, links to similar features in other apps, user documentation. |
| `type` | Always `Feature`, matching section 2. |

## Process

### 1. Frame the feature

1. Read the user's prompt and any linked material (requests, tickets, screenshots, documents).
2. Settle the [document location](#document-location), and read the existing documents there (their `index.md` first when there is one).
3. Look for the [similar or related features in the app](#similar-or-related-features-in-the-app). Keep your findings to present in step 5, and note any other app the input mentions as an example, for the interview.
4. List every distinct subject you see in the input. For each, note whether it is a feature, a bug fix or a refactoring.
5. Propose to the user:
   - a title for the document;
   - the main feature and a one-sentence draft of the intent;
   - the similar or related features you found, with your questions about them;
   - the side issues you identified (other features, bug fixes, refactorings), asking for each: separate document or later discussion;
   - when the request already looks too large for one document, a split into [sub-features](#sub-features).
6. Do not continue until the user confirms the feature and its intent. The title is a working title: propose a better one whenever the discussion sharpens the subject.
7. Confirm the filename with the user, in the agreed [document location](#document-location), and [create the document](#live-draft) right away.

### 2. Interview

Work through the sections in order (see [template.md](template.md)), filling the gaps with questions, and [update the document](#live-draft) as each answer is confirmed.

- Ask one question at a time by preference, and never more than 3 at a time; this applies to every step, including framing and review. Use your structured question tool (e.g. `AskUserQuestion` in Claude Code) when the answer is a choice between concrete options; use plain questions for open-ended context.
- Offer your proposed answer where you have one, so the user can just confirm or correct it.
- Cover: who asked for it and why now; the problem it solves; how people cope today; who uses it and who else is affected; what "done" looks like for them; how the user will tell it worked.
- Hunt down vague words ("easy", "fast", "better", "some users", "etc.") and replace them with concrete statements that anyone can check by using the product.
- When the user answers in technical terms, ask what people will do or see as a result, and write that instead; ask whether the technical point itself is a requirement (see Rules).
- Unusual situations: propose the ones you can think of one at a time (or in small groups) with a suggested behaviour, and ask the user to confirm, correct or add to them: nothing to show yet, a mistake or a change of mind, missing permission, a limit reached, several people at once, no connection. Only the ones the user confirms become acceptance criteria.
- [Rollout](#rollout): ask whether the feature ships in one go or in stages, and agree on the stages before the acceptance criteria, so that the criteria can be grouped by stage.
- Acceptance criteria: derive the needs from the personas and the intent, propose them for validation, and agree on criteria in Given / When / Then form that anyone can check by using the product.
- [Similar features in other apps](#similar-features-in-other-apps): ask for examples early, and for each one's screenshots, screen captures and links.
- [Screenshots, mockups and prototypes](#screenshots-mockups-and-prototypes): ask whether the user wishes to add any.
- When an answer reveals a new subject, treat it as a side issue (see Rules): don't widen the document.
- Keep a running list of open questions. The interview is done when every section is written without an assumption and no section is still marked pending.

### 3. Finish the draft

The document already holds every confirmed answer. Go over it as a whole:

1. Check that it follows [template.md](template.md) exactly: same frontmatter, same sections, same order, `status: draft`, and no section still marked pending.
2. Every statement must be specific and checkable by someone using the product. If it can't be, rewrite it or ask.
3. Read it as the people in Personas would: rewrite any word they wouldn't use.
4. Read it as the author of the technical spec would: list every product question the spec would still have to ask, and ask the user each one (see Rules).
5. If the user explicitly chooses to defer a question, mark it inline as `> **Open question:** …`, never fill it with a guess.

### 4. Review with the user

1. Walk the user through the document section by section, in the file they have been following, and ask for corrections.
2. Iterate until the user says the draft is ready for review. The document stays `status: draft`: approving its content is the reviewers' job, in the pull request.
3. Update the bundle's `index.md` and `log.md` if they exist (or were created at the user's request), as described in the specs skill, and run the validator if available.
4. Publish the draft as a pull request, following the [publishing section of the specs skill](../specs/SKILL.md#publishing), with this document and its screenshots in place of the spec (e.g. branch `docs/<document-id>`, commit `docs(features): add <document-id>`). Its description gives the feature's intent, a link to the file, the open questions and the side issues.
5. In your final reply, give the pull request's URL, and list:
   - any open questions left in the document;
   - the side issues and what the user decided for each (separate document / later discussion), so nothing is lost;
   - the sub-features agreed, with their stage, and which documents are still to be written;
   - the technical points the user mentioned that aren't functional requirements, for the spec;
   - the next step: a technical spec written from this document with the specs skill (e.g. `/sdlc:specs <path to the document>` in Claude Code).

## Quality checklist

Before presenting the draft, check that:

- [ ] Exactly one feature; no bug fix and no refactoring.
- [ ] The intent fits in one to three sentences and a newcomer understands it.
- [ ] Similar or related features in the app: each one found is listed with the user's decision (distinct, intended overlap, pattern followed, or how they work together), or "None found" is confirmed by the user.
- [ ] Similar features in other apps: the user was asked for examples, and for each one's screenshots, screen captures and links; each example says what to take from it and what not, in the user's words; or "None" is confirmed by the user.
- [ ] Every persona has at least one concrete gain or loss; negative impacts are stated, not hidden.
- [ ] Every outcome is observable, and measurable where the user gave a measure.
- [ ] Boundaries list what is explicitly out of scope, including the side issues left out.
- [ ] Rollout: one sub-section per stage when there are several, each saying what it delivers, who gets it and when it is done.
- [ ] Sub-features: each one is a stage (or part of a stage) of the parent's rollout, linked from it once its document exists; a sub-feature document has a `part-of` link to its parent and the line `Part of [<parent title>](/<path to the parent>.md).` in section 2. No sub-feature is listed among the similar or related features.
- [ ] Every acceptance criterion names a persona from section 4, is in Given / When / Then form, can be checked by using the product, and, with several stages, sits under the stage that delivers it.
- [ ] Screenshots, mockups and prototypes: every screenshot file is in the bundle, shown in the document and listed in `media` with its hash; every hosted screenshot, mockup and prototype is linked; or "None" is confirmed by the user.
- [ ] Complete for the spec: the specs skill could write a complete, exhaustive technical spec from the document without asking a single product question; any deferred one is an open question in the document.
- [ ] No `> **Pending:**` marker is left.
- [ ] Nothing was added that the user didn't confirm.
- [ ] No technology, component, architecture or code, and no technical assumption; any technical point is a functional requirement the user confirmed, in plain language.
- [ ] A non-technical reader understands every sentence.
- [ ] The document is in the location the user confirmed.
- [ ] The document is `status: draft`, with no new `verified` entry: only the merge of its pull request makes it `stable`.
- [ ] AI-XF Level 2: frontmatter is parseable YAML with `type: Feature` and a unique, well-formed `id`; `generated` is present; every `links` entry has `rel` and a resolvable `to`, mirrored by a body link; every `media` entry has a `uri`; timestamps have a UTC offset; footnotes match `sources[].id`; the filename isn't `index.md` or `log.md`; the bundle has `manifest.ai-xf.yaml`.

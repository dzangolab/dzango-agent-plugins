---
name: specs
description: Interview the user to turn a messy, ambiguous request into a single high-quality technical spec document (AI-XF 0.5 markdown, a superset of OKF 0.2) for exactly one feature, bug fix, or refactoring. Use when the user asks to write, draft, create, or refine a spec, technical spec, specification, or requirements for a change, or invokes the specs skill directly (e.g. /sdlc:specs in Claude Code).
---

# Specs

Produce one technical spec document, in markdown conforming to AI-XF 0.5 (see [AI-XF format](#ai-xf-format)), that makes the next SDLC steps (test plan, implementation plan, implementation) easy and reliable.

The user's input will be messy, unclear, ambiguous and full of holes. Your first job is to **ask questions and clarify intent**, not to write. Never assume: every fact, choice and inference that ends up in the spec must be validated by the user.

## Rules

- **One subject per spec.** A spec covers exactly one feature, one bug fix, or one refactoring.
- **One type per spec.** A spec is `feature`, `bug fix`, or `refactoring` — never a mix. If the work blends types (e.g. "refactor X and add Y"), the main focus gets this spec; the rest is split off.
- **Pick the focus of the conversation.** Choose the subject and type that appear to be the main focus. If it isn't clear, ask — don't guess.
- **Side issues never leak in.** Other features, bug fixes or refactorings — mentioned by the user or discovered by you while exploring the code — are not added to the spec. For each one, ask the user whether to:
  1. split it into its own spec document (written after this one, in a separate pass), or
  2. park it for a later discussion.
- **Validate, don't assume.** When you infer something (from the code, the conversation, or common practice), present it as a proposal and get a yes/no before writing it down.
- **Watch out for unjustified couplings.** When the request, the code you explore, or the spec itself makes one component depend on another — or know about its internals — without a clear reason, flag it to the user. Typical signs: a generic or shared package that knows about a specific app or consumer, a component reaching into another's internals instead of its public API, a new circular dependency, two components that must change or ship together, or a feature that only works with one particular implementation of a dependency. Ask why the coupling is needed: if the user justifies it, record the reason in the spec (Constraints or Blast radius); if not, agree with the user on a decoupled outcome to specify instead.
- **What, not how.** The spec states the problem, the expected outcome and its limits. It does not contain an implementation plan, task breakdown, or code — those are later steps.

## Codebase scope

Before exploring any code, settle what the analysis covers. Propose a scope, inferred from the request, the linked material and the working directory, and confirm it with the user:

| Scope | Typical signs | What it covers |
| --- | --- | --- |
| Single repository | The working directory is in a git repository that isn't a monorepo. | The repository. |
| App or package in a monorepo | Workspace configuration at the repository root: `pnpm-workspace.yaml`, `workspaces` in `package.json`, `turbo.json`, `nx.json`, `lerna.json`, `go.work`, a Cargo `[workspace]`. | The app or package, the workspace packages it depends on and those that depend on it, and the shared root configuration. |
| Several repositories | The subject spans repositories: the working directory holds sibling repositories (e.g. all the repositories of a product family), or the request names components that live in other repositories. | Each repository the user includes, listed by name. |

- **Never widen the scope silently.** A component found outside the scope (e.g. a consumer in another repository) is raised with the user, who decides whether to add it to the scope.
- **Record the scope** in the document: each repository, with the app or package path for a monorepo, and the branch and commit analysed, so a reader knows what the analysis is based on. List anything in scope that wasn't analysed, and why.
- **Local working copies are preferred, not required.** A local working copy is fast to search, gives the current state of the code, and is the only way to use a code index such as CodeGraph, when you have one (with CodeGraph, pass each repository's path as `projectPath` when it has a `.codegraph/` directory). Look for each repository next to the current one, or ask the user for its path. When a repository has no local working copy, ask the user to choose:
  1. give its path, or clone it to a location they choose;
  2. read it remotely (e.g. `gh api`, `gh search code`): slower and partial, without a code index, so findings from it are marked as lower confidence;
  3. leave it out: it is listed as not analysed, and its components in the blast radius are marked unverified.
- **Don't change a working copy.** No pull, checkout, stash or clone without the user's approval. Check each working copy's branch and uncommitted changes, and tell the user when it isn't on the branch the work is about, or looks out of date.

## Spec location

Right after the codebase scope (or first, outside a codebase), settle the folder the specs live in: it is the AI-XF bundle, and the existing specs in it are needed to spot related specs, prerequisites and contradictions, and to keep `id`s unique.

1. Infer candidates, in this order:
   - an AI-XF bundle already in or around the scope: a folder with a `manifest.ai-xf.yaml`;
   - a folder that already holds specs: markdown files with spec frontmatter (`type: Feature`, `Bug fix` or `Refactoring`), in the current folder or a sub-folder (`specs/`, `docs/specs/`, or any other);
   - a spec folder convention stated in the project (agent instructions such as `AGENTS.md`, `CLAUDE.md` or `GEMINI.md`, `README.md`, contributing guide);
   - for a scope with several repositories, the same signs in the folder that holds them (e.g. a `specs/` folder next to the repositories), since the specs then span repositories.
2. Always confirm with the user: propose the best candidate, and mention the others when there are several.
3. If you find no suitable candidate, ask the user where the specs should live; don't pick a default.

The spec file goes in the agreed folder as `<type>-<slug>.md`, where `<type>` is `feature`, `bugfix` or `refactoring`, unless the folder already follows another naming convention.

## AI-XF format

The spec is an [AI-XF 0.5](https://github.com/DavidROliverBA/ai-xf-format/blob/main/SPEC.md) concept, conforming at **Level 2 (AI-XF Full)**. AI-XF is a strict superset of [OKF 0.2](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md), so the spec is also a valid OKF concept: a UTF-8 markdown file with a YAML frontmatter block (opened and closed by `---`), followed by the body. The spec folder is the AI-XF bundle. See [template.md](template.md) for the exact layout.

Frontmatter (keys in alphabetical order):

| Key | Required | Value |
| --- | --- | --- |
| `description` | Recommended | One-line summary of the intent. |
| `generated` | Yes (trust field, satisfies Level 2) | `by: <agent>/<version>`, the agent writing the spec and its version (e.g. `claude-code/2.1.314` from `claude --version`), `at: <ISO 8601 datetime>`. |
| `id` | Yes (Level 1) | Lowercase, words separated by `-`, no `/`, unique in the bundle; same as the filename stem (`<type>-<slug>`). Never change it once assigned, even if the file is renamed or moved. |
| `links` | When related specs exist | Typed edges `{rel, to, note}`; `to` is the target's `id`. Typical `rel`: `depends-on`, `references`, `relates-to` (e.g. a side issue split into its own spec), `supersedes`. |
| `sources` | When there are sources | List of `{id, resource, title}`; `resource` is required (absolute URL, `/`-prefixed bundle path, or relative path). Use for functional requirements, issues, similar features in other apps, screenshots, logs. |
| `status` | Yes | `draft` while iterating; `stable` once the user approves; `deprecated` for a tombstone. |
| `tags` | Recommended | Short strings: area, module, product. |
| `title` | Recommended | The spec title, identical to the H1. |
| `type` | **Required** (Level 0) | Exactly one of `Feature`, `Bug fix`, `Refactoring`, matching section 2. |
| `verified` | On approval | `- by: human:<id>`, `at: <ISO 8601 datetime>`. |

Rules to respect:

- All timestamps include an explicit UTC offset (e.g. `2026-10-29T14:05:00+01:00`); get them from `date -Iseconds`.
- Actors: `<producer>/<version>` for tools, `human:<id>` for people. Content confirmed by the user is attested with `human:`. Ask the user for their `<id>` once (propose their git `user.email`).
- Every footnote in the body (`[^id]`) matches a `sources[].id`, and every source cited in the body has an `id`.
- Every `links` entry targets a spec that exists in the bundle, and is mirrored by at least one plain markdown link to the same spec in the body (usually in Context). Body links to other specs use `/`-prefixed paths from the bundle root; other links use absolute URLs or relative paths.
- The file must never be named `index.md` or `log.md` (reserved).
- Tombstones: never delete a spec. A replaced or retired spec gets `status: deprecated`, a `superseded-by` or `merged-into` link to its successor if any (mirrored in the body), and a `Deprecation` or `Merge` entry in `log.md`.

Bundle files at the spec folder root:

- `manifest.ai-xf.yaml` (required for Level 1): `ai-xf: "0.5"`, `name: <bundle-name>`, `conformance: 2`. If it is missing, propose creating it and confirm the name with the user.
- `index.md`: headed sections of list entries linking to each spec with its description; no frontmatter except an optional `okf_version: "0.2"`.
- `log.md`: `## YYYY-MM-DD` headings, newest first; prose entries starting with one of the bold leading words `Initialization`, `Creation`, `Update`, `Merge`, `Split`, `Deprecation`, `Contradiction`, `Resolution`, `Gap`. Read existing entries and add to them; never regenerate the log. Log lifecycle events only, so the log stays short:
  - add an entry for a spec's creation (with its status), its approval (`draft` → `stable`), a split, a merge, a deprecation, a contradiction or resolution between stable specs, a gap, or a bundle-wide change (e.g. a folder reorganization);
  - never log edits to a `draft` spec: git history covers them;
  - use `Update` only for a change to a `stable` spec;
  - write one entry per spec per event, combining the changes made in the same session;
  - if the log still grows too long, split it by folder (each folder gets its own `log.md`, the root one keeps the bundle-wide events) rather than archiving it: any other `.md` file would count as a concept.
- If `index.md` or `log.md` don't exist, ask the user whether to create them.

Validation: if the user has the AI-XF validator (`tools/ai-xf-validate.py` from the AI-XF repo, Python 3.9+), ask for its path and run `python3 <path>/ai-xf-validate.py <bundle-dir> --level 2`; exit code 0 means pass. Don't download and run it without the user's approval.

## Process

### 1. Frame the subject

1. Read the user's prompt and any linked material (issues, screenshots, files).
2. If the context refers to formal functional requirements (a requirements document, FR IDs, acceptance criteria, user stories), read them and use them as inputs: they are the starting point for the spec, and the spec must stay consistent with them. Reference requirement IDs where relevant, and raise any gap or contradiction with the user rather than resolving it silently.
3. If working in a codebase, settle the [codebase scope](#codebase-scope).
4. Settle the [spec location](#spec-location), and read the existing specs there (their `index.md` first when there is one) to spot related specs.
5. If working in a codebase, explore the relevant code within the scope to ground your questions (what exists today, where the pain is). Treat findings as hypotheses to confirm, not facts.
6. List every distinct subject you see in the input. For each, note its likely type.
7. Propose to the user:
   - a title for the spec;
   - the main subject and its type, and a one-sentence draft of the intent;
   - the side issues you identified, asking for each: separate spec or later discussion.
8. Do not continue until the user confirms the subject and type. The title is a working title: propose a better one whenever the discussion sharpens the subject, and confirm the final title before writing the file.

### 2. Interview

Work through the sections in order (see [template.md](template.md)), filling the gaps with questions.

- Ask one question at a time by preference, and never more than 3 at a time; this applies to every step, including framing and review. Use your structured question tool (e.g. `AskUserQuestion` in Claude Code) when the answer is a choice between concrete options; use plain questions for open-ended context.
- Offer your proposed answer where you have one, so the user can just confirm or correct it.
- Probe for what's missing: edge cases, error states, permissions, empty states, data volumes, platforms, backwards compatibility, migration, performance, accessibility, security.
- Hunt down vague words ("fast", "simple", "better", "some users", "etc.") and replace them with concrete, verifiable statements.
- When an answer reveals a new subject, treat it as a side issue (see Rules) — don't widen the spec.
- Edge cases: propose the edge cases you can think of one at a time (or in small groups) with a suggested expected behaviour, and ask the user to confirm, correct or add to them.
- User stories: derive them from the personas and the intent (and from formal requirements, when available), propose them for validation, and agree on acceptance criteria in Given / When / Then form that a test can check.
- Rollout: ask whether the change ships in one go or in phases. For phases, agree on their order, what each delivers, who gets it, and its exit criteria. Phases split the delivery of this one subject; they never smuggle in another feature, bug fix or refactoring.
- Prerequisites: for each phase, ask what must be in place before work on it starts (other specs, infrastructure, access, data, decisions). Record "None" only once the user confirms nothing is needed. A prerequisite that is another spec in the bundle also gets a `depends-on` link.
- Blast radius (existing systems): explore the code to find the components likely to be impacted (callers, dependants, shared contracts, data, config, tests; use a code index's impact analysis, such as CodeGraph's blast-radius summary, when you have one and the repo is indexed), across every repository in the codebase scope. Present them to the user as hypotheses, together with the kind of impact, and ask what you missed — the user often knows about consumers outside the scope.
- Keep a running list of open questions. The interview is done when every section can be written without an assumption.

Type-specific questions to cover:

- **Feature:** who asked for it and why now; the pain point it resolves; how users cope today; similar features in other apps (links); what "done" looks like for the user.
- **Bug fix:** exact steps to reproduce; expected vs actual behaviour; environment (version, browser/OS, config, data); frequency and severity; when it started (regression? which change?); logs, errors, screenshots; known workarounds.
- **Refactoring:** the code-quality pain (duplication, coupling, testability, performance, readability); which modules are in scope; what must stay strictly identical (public API, behaviour, outputs, performance budget); how equivalence will be proven.

### 3. Draft

1. Write the spec following [template.md](template.md) exactly: same frontmatter, same sections, same order. Set `status: draft`.
2. Prefer diagrams to prose in the workflow / user journey section (Mermaid: `flowchart`, `sequenceDiagram`, `stateDiagram-v2`), with short text only where the diagram needs explaining.
3. Every statement must be specific and testable. If a statement can't be verified by a test or a reviewer, rewrite it or ask.
4. If the user explicitly chooses to defer a question, mark it inline as `> **Open question:** …` — never fill it with a guess.

### 4. Review with the user

1. Before writing the file, confirm its filename with the user, in the agreed [spec location](#spec-location).
2. Write the file, then walk the user through it section by section and ask for corrections.
3. Iterate until the user approves. On approval, set `status: stable` and add the `verified` entry for the user.
4. Update the bundle's `index.md` and `log.md` if they exist (or were created at the user's request), and run the validator if available.
5. In your final reply, list:
   - any open questions left in the spec;
   - the side issues and what the user decided for each (separate spec / later discussion), so nothing is lost.

## Quality checklist

Before presenting the draft, check that:

- [ ] Exactly one subject and one type.
- [ ] The intent fits in one to three sentences and a newcomer understands it.
- [ ] Every persona has at least one concrete gain or loss; negative impacts are stated, not hidden.
- [ ] Constraints are hard, verifiable requirements; boundaries list what is explicitly out of scope.
- [ ] For a bug fix: reproduction steps, expected and actual behaviour are present.
- [ ] For a refactoring: constraints include "no new feature" and "no behaviour change" (unless the user explicitly says otherwise), and how equivalence is checked.
- [ ] Every edge case has a verifiable expected behaviour.
- [ ] Every user story names a persona from section 4 and has Given / When / Then acceptance criteria.
- [ ] Rollout: one sub-section per phase when there are several, each with verifiable exit criteria.
- [ ] Prerequisites: one sub-section per Rollout phase when there are several; "None" where nothing is required, confirmed by the user.
- [ ] The workflow / user journey has at least one diagram.
- [ ] Blast radius: for an existing system, every impacted component is named as in the code, with its kind of impact, and validated by the user; for a new system, it says "Not applicable: new system."
- [ ] The spec is in the spec location the user confirmed.
- [ ] The codebase scope is confirmed by the user and recorded in Blast radius, with the branch and commit analysed per repository, and anything in scope that wasn't analysed.
- [ ] No unjustified coupling between components: every dependency the spec introduces or relies on has a reason the user validated.
- [ ] No implementation plan, task list or code.
- [ ] Nothing in the spec is an unvalidated assumption.
- [ ] AI-XF Level 2: frontmatter is parseable YAML with a non-empty `type` matching section 2 and a unique, well-formed `id`; `generated` is present; every `links` entry has `rel` and a resolvable `to`, mirrored by a body link; timestamps have a UTC offset; footnotes match `sources[].id`; the filename isn't `index.md` or `log.md`; the bundle has `manifest.ai-xf.yaml`.

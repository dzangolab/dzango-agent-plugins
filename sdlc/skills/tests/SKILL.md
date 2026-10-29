---
name: tests
description: Turn a given technical spec (a single markdown file, such as one written by /sdlc:specs) into a test plan — no code — listing the meaningful tests to write, grouped by level from end-to-end acceptance tests (Gherkin) down to unit tests, each traced to the spec. Asks about every gap in the spec instead of assuming. Use when the user asks for a test plan, testing plan, test strategy or test cases for a spec, or invokes /sdlc:tests.
---

# Tests

Produce one test plan document for one spec: the tests worth writing, grouped by level from the widest scope (end-to-end acceptance) down to unit tests, each one traced to the spec item it proves. The plan is the input for the next SDLC step, generating test code, so it must be:

- **Easy to validate.** A reviewer can check the plan against the spec without reading code: acceptance tests in Gherkin, all other tests as one table row each, and a coverage matrix that shows what each spec item is proven by.
- **Easy to turn into code.** Every test names its level, the subject under test as it is named in the code, its setup, its action, its expected result and its test doubles, so a code generator has nothing to guess.

The plan contains no test code, apart from the Gherkin scenarios, which are a specification language.

## Rules

- **The spec must be given.** The input is one spec, given as a path to (or the content of) a single markdown file. Without one, ask for it; never build a test plan from the conversation, an issue or the code alone. If the spec spans several files or isn't markdown, say so and ask the user for a single markdown file.
- **Unusable spec: offer `/sdlc:specs` first.** When no spec is available, or the one supplied can't support a test plan (it isn't a spec, it's mostly gaps, it mixes several subjects or types, or answering its gaps would amount to writing it), explain why in a few words and offer to write the spec first with `/sdlc:specs`, using the supplied material as its input. If the user accepts, run `/sdlc:specs`, then plan from the approved spec it produces. If the user declines, go on with the gaps process only if the spec has enough to test; otherwise stop.
- **Any spec format.** A spec written by `/sdlc:specs` (see [its template](../specs/template.md)) maps directly onto the plan. For any other spec, map its content onto the same concepts (intent, type, constraints, boundaries, edge cases, user stories or requirements and their acceptance criteria, rollout, blast radius) and confirm the mapping with the user before planning.
- **One plan per spec.** The plan tests exactly the spec's subject. Tests for anything else (a bug you noticed, a missing test elsewhere) are side issues: tell the user and note them in your final reply; don't add them to the plan.
- **Don't invent, don't assume.** Every expected result, threshold, input value, data set, error message and permission in the plan comes from the spec, the code, or the user's answer. When the spec is silent, vague or contradictory about something a test needs, ask (see [Gaps](#gaps)). You may offer a proposed answer for the user to confirm, but never write an unconfirmed one into the plan.
- **Quality over quantity.** Plan the smallest set of tests that proves the spec and guards against its real risks:
  - Every test traces to at least one spec item. A test that proves nothing in the spec doesn't belong in the plan.
  - Test each behaviour at the lowest level that can prove it. Use the widest levels only for what only they can prove: the user journeys, and the wiring between components.
  - No duplicate coverage across levels, unless the user agrees there's a reason for it (e.g. the most critical journey).
  - One behaviour per test, with one expected result that can fail.
  - Use equivalence classes and boundary values instead of listing cases; use a Scenario Outline or a single parameterised row instead of near-identical tests.
  - Don't test framework or library behaviour, trivial code (getters, pass-throughs), or implementation details that a refactoring could change without changing behaviour.
  - List what you deliberately don't test, and why, in the "Not tested" section, so the reviewer can challenge it.
- **Plan, don't code.** No test code, step definitions, fixtures code or mocks code. Name what is needed; the code comes later.
- **The spec is the source of truth.** Don't edit the spec without the user's approval. When an answer to a gap changes or completes the spec, propose updating the spec as well (with `/sdlc:specs` for a spec it wrote), so that the spec and the plan stay consistent.

## Gaps

A gap is anything a test needs that the spec doesn't settle. Look for:

- a statement that can't be verified ("fast", "user-friendly", "handles errors gracefully", "etc.");
- a user story with no acceptance criteria, or criteria with no observable outcome;
- an edge case or error path with no expected behaviour (empty, invalid, too large, concurrent, unauthorised, timeout, offline, partial failure);
- a threshold with no number or unit (performance, limits, retention, retries);
- an input, data set or role the tests need but the spec doesn't define;
- two statements that contradict each other, or a statement that contradicts the code;
- a constraint, rollout exit criterion or blast-radius impact that no one says how to verify;
- for a bug fix: missing reproduction steps, or expected behaviour that isn't stated;
- for a refactoring: no definition of "behaviour unchanged" (which outputs, APIs, performance), or no way to prove equivalence.

Ask about gaps one question at a time by preference, and never more than 3 at a time. Use `AskUserQuestion` when the answer is a choice between concrete options, and plain questions otherwise. Start with the gaps that block the most tests. If the user explicitly defers a gap, write it inline in the plan as `> **Open question:** …` next to the tests it blocks, and mark those tests **Blocked** in the coverage matrix; never fill it with a guess.

## Test levels

Group the tests by level, from the widest scope to the narrowest, and use only the levels that the spec and the system need:

| Level | Proves | Format |
| --- | --- | --- |
| Acceptance (end-to-end) | A user story's acceptance criteria and key edge cases, through the system's real entry point (UI, public API, CLI), in the user's language. | Gherkin |
| Non-functional | Constraints on performance, security, accessibility, compatibility, reliability, observability. | Table |
| Integration | Two or more real components working together: API contracts, database, queues, external services, configuration. | Table |
| Component | One component (module, service, UI component) in isolation, its collaborators replaced by test doubles. | Table |
| Unit | One function, class or rule, including its boundary values and errors. | Table |
| Regression | Existing suites to re-run because the blast radius touches them. | List |

Type-specific emphasis:

- **Feature:** acceptance tests for each user story; lower levels for the rules, edge cases and error paths.
- **Bug fix:** one test that reproduces the bug at the lowest level that can show it, marked "must fail before the fix and pass after it", plus an acceptance test for the user story the bug breaks; regression tests around the fix.
- **Refactoring:** characterisation tests that pin the current behaviour, to be written and green **before** the refactoring, at the level of the boundaries that must not change (public API, outputs); existing suites listed under Regression. No tests for new behaviour: a refactoring has none.

## AI-XF format

The test plan uses the same document format as the specs: an [AI-XF 0.5](https://github.com/DavidROliverBA/ai-xf-format/blob/main/SPEC.md) concept at Level 2 (and therefore an OKF 0.2 concept). Follow the [AI-XF format section of the specs skill](../specs/SKILL.md#ai-xf-format) for frontmatter rules, timestamps, actors, footnotes, links, reserved file names, bundle files and validation, with these differences:

- `type: Test plan`.
- `id` and filename stem: `<spec-id>-test-plan` (e.g. `feature-csv-export-test-plan`); for a spec without an `id`, use its filename stem.
- `title`: `Test plan: <spec title>`, identical to the H1.
- The spec under test is referenced with a `references` link to its `id` when it is in the same bundle (mirrored by a body link in section 1), or with a `sources` entry otherwise.
- `status`: `draft` while iterating, `stable` once the user approves. When the spec changes after the plan was approved, the plan goes back to `draft` until it's updated.

## Process

### 1. Load the spec

1. Get the spec from the user (see Rules), read the whole file, and read the local material it links to (requirements, sources, related specs). If it's missing or unusable, offer to write it first with `/sdlc:specs` (see Rules).
2. Identify its format (`/sdlc:specs` or other), type, and status. If the spec isn't approved (e.g. `status: draft`), tell the user the plan may need rework, and ask whether to go ahead.
3. If the plan already exists, read it and update it rather than starting over.

### 2. Inventory

1. List every testable spec item, with a reference the plan can use:
   - the spec's own IDs when it has them (`US-1`, `FR-12`);
   - otherwise, IDs counted in document order: `US-n/AC-m` for the m-th acceptance criterion of user story n, `EC-n` for the n-th edge case, `C-n` for the n-th constraint, `R-n` for the n-th rollout exit criterion, `BR-n` for the n-th blast-radius component.
   State the convention used in the plan's Coverage section.
2. If working in a codebase, explore it (CodeGraph first when the repo is indexed): the existing test frameworks, runners, folders and naming conventions per level; the existing tests around the blast-radius components; how test data and test doubles are handled today. Treat findings as hypotheses to confirm, not facts.
3. Present to the user, briefly: the mapping (for a spec not written by `/sdlc:specs`), the number of testable items per section, and the gaps found. Then work through the gaps (see [Gaps](#gaps)).

### 3. Agree on the strategy

Propose, and get confirmation for:

- the levels to use, and why any are left out;
- the tool and location for each level, taken from the codebase's existing setup (if there's none for a level, ask the user rather than picking one);
- what each level replaces with test doubles, and what it runs for real;
- the test data and environments the tests need.

### 4. Design the tests

1. Start from the acceptance criteria and work down: decide, for each spec item, the lowest level that can prove it, and add a wider test only where the item can't be proven lower.
2. Write the acceptance tests in Gherkin, in declarative style: describe what the user does and sees in the domain's words, not clicks, selectors, URLs or internal names. One `Feature` per spec. One `Scenario` per acceptance criterion or edge case, or a `Scenario Outline` with `Examples` for equivalence classes. Tag each scenario with its test ID and the spec items it covers (`@AT-1 @US-1/AC-1`). Reuse the same wording for the same step, so step definitions can be shared.
3. Write each other test as one table row: ID, subject under test (as named in the code: path, module, function, endpoint, component), Given, When, Then, test doubles, and the spec items covered.
4. Fill the coverage matrix: every testable spec item has at least one test, is marked **Blocked** by an open question, or is listed under "Not tested" with a reason the user agrees with.
5. Run through the [quality checklist](#quality-checklist) and remove tests that don't earn their place.

### 5. Review with the user

1. Confirm the file location with the user: propose the spec's folder (the same AI-XF bundle), next to the spec, named `<spec-id>-test-plan.md`, or follow an existing convention in the project.
2. Write the file with `status: draft`, following [template.md](template.md) exactly: same frontmatter, same sections, same order. Then walk the user through it section by section, starting with the coverage matrix, and ask for corrections.
3. Iterate until the user approves. On approval, set `status: stable` and add the `verified` entry for the user.
4. Update the bundle's `index.md` and `log.md` if they exist, and run the validator if available (see the specs skill).
5. In your final reply, list:
   - the open questions left in the plan, and the tests they block;
   - the gaps found in the spec, and whether the spec was updated;
   - any side issues you noticed.

## Quality checklist

Before presenting the draft, check that:

- [ ] The plan covers exactly one spec, given by the user.
- [ ] Every testable spec item is in the coverage matrix, with a test, **Blocked**, or a reason under "Not tested".
- [ ] Every test traces to at least one spec item; no orphan tests.
- [ ] Every expected result, threshold and data value comes from the spec, the code or the user; nothing invented.
- [ ] Each behaviour is tested at the lowest level that can prove it; no duplicate coverage across levels without an agreed reason.
- [ ] Every test checks one behaviour, with an expected result that can fail.
- [ ] Equivalence classes and boundary values replace lists of near-identical tests.
- [ ] Levels are ordered from the widest to the narrowest; an unused level says "Not used: see Strategy.", and the Strategy gives the reason.
- [ ] Acceptance tests are declarative Gherkin, tagged with test ID and spec items, with consistent step wording.
- [ ] Every other test names its subject as in the code, and has Given, When, Then and test doubles.
- [ ] For a bug fix: a reproducing test marked "must fail before the fix and pass after it".
- [ ] For a refactoring: characterisation tests to write before the change, existing suites under Regression, and no tests of new behaviour.
- [ ] Regression lists the existing suites touched by the blast radius.
- [ ] Tools, locations and test doubles per level come from the codebase or the user.
- [ ] No test code besides Gherkin.
- [ ] AI-XF Level 2: same checks as the specs skill, with `type: Test plan` and a resolvable link or source to the spec.

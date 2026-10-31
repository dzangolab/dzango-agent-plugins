---
name: plan
description: Turn a given technical spec (a single markdown file, such as one written by the specs skill) into an implementation plan, without code — an ordered list of small steps, each producing a testable deliverable, depending only on previous steps, and touching one or two sub-systems. When a test plan exists for the spec, the plan's constraint is that its tests pass. Use when the user asks for an implementation plan, a build plan, or the steps to implement a spec, or invokes the plan skill directly (e.g. /sdlc:plan in Claude Code).
---

# Plan

Produce one implementation plan document for one spec: the ordered steps that take the system from where it is today to the spec being delivered. The plan is the input for the next SDLC step, implementation, so a developer or an agent can pick up the next step and work on it knowing only the previous steps are done.

The plan outlines the work. It contains no code.

## Step rules

Every step must meet all of these. They are what makes the plan worth having, so check each step against them (see [Check the steps](#5-check-the-steps)) and reshape any step that fails.

- **It produces a deliverable: a testable functionality.** When the step is done, something works that didn't before (or, in a refactoring, the same behaviour rests on the new structure), and a test can show it. "Create the table", "add the types" or "write the helper" aren't deliverables on their own: combine them with the behaviour they enable. The functionality doesn't have to reach an end user: it can be observed at the boundary of the sub-system that delivers it (an endpoint answering, a job producing its output, a component rendering its states).
- **It depends only on previous steps.** A step relies on nothing but the state of the system before step 1, plus the steps before it. It never needs a later step to compile, pass its tests or work, and it relies on no work outside the plan: see [Prerequisites](#prerequisites).
- **It has a limited scope.** It ideally touches no more than one or two sub-systems (see [Sub-systems](#sub-systems)), and it's small enough to review as one pull request. When a step needs more, first try another slice (see [Slicing the work](#slicing-the-work)). If it truly can't be split, keep it only with the user's agreement, and record the reason in the step.
- **It leaves the system working.** Everything that was passing before the step still passes after it, and the step can be merged and shipped on its own. Functionality that isn't ready for users stays hidden, by a means the spec or the user has agreed (e.g. a feature flag), not by leaving things broken.

Prefer slicing by functionality to slicing by layer: "export one report as CSV, through the API" is a step; "all the database work" isn't. When one piece of functionality crosses more than two sub-systems, slice it at a sub-system boundary instead, with the deliverable tested at that boundary: first the API endpoint, then the UI that calls it.

## Rules

- **The spec must be given.** The input is one spec, given as a path to (or the content of) a single markdown file. Without one, ask for it; never build a plan from the conversation, an issue or the code alone. If the spec spans several files or isn't markdown, say so and ask the user for a single markdown file.
- **Assume a well-formed spec, and check that it's suitable.** A spec is usually written with the `specs` skill and needs no interview. It's unsuitable when it isn't a spec, it mixes several subjects or types, it doesn't state the expected outcome, or too much of what the steps need is missing. In that case, or when no spec is available, explain why in a few words and offer to go through the `specs` skill first, using the supplied material as its input. If the user accepts, run the `specs` skill, then plan from the spec it produces. If the user declines, go on only if the spec has enough to plan from; otherwise stop.
- **Any spec format.** A spec written by the `specs` skill (see [its template](../specs/template.md)) maps directly onto the plan. For any other spec, map its content onto the same concepts (intent, type, constraints, boundaries, edge cases, user stories or requirements, rollout, prerequisites, blast radius) and confirm the mapping with the user before planning.
- **A test plan is the plan's constraint.** See [Test plan](#test-plan).
- **Same codebase scope as the spec.** Plan the work in the repositories the spec's codebase scope covers. Take the scope from the spec (a spec written by the `specs` skill records it in Blast radius) and confirm it with the user; when the spec doesn't state it, settle it as described in the [codebase scope section of the specs skill](../specs/SKILL.md#codebase-scope), which also covers repositories without a local working copy. Work needed in a repository outside the scope is raised with the user, never added silently.
- **One plan per spec.** The plan implements exactly the spec's subject and respects its Boundaries. Anything else (a bug you noticed, a clean-up you'd like to do) is a side issue: tell the user and note it in your final reply; don't add steps for it. Preparatory work belongs in the plan only when a step needs it.
- **The plan decides the how; the user validates it.** The spec states what, so this is where technical choices are made. Name the options when there's a real choice, recommend one, and get the user's confirmation; record each one under Approach, with its reason. Follow the patterns the codebase already has, unless the user agrees otherwise.
- **Don't invent, don't assume.** Every component in the plan is named as in the code, or marked as new. When the spec is silent on something a step needs, ask, one question at a time by preference and never more than 3 at a time, using your structured question tool (e.g. `AskUserQuestion` in Claude Code) when the answer is a choice between concrete options. If the user explicitly defers a question, write it inline as `> **Open question:** …` next to the steps it blocks, and mark those steps **Blocked**; never fill it with a guess.
- **Watch out for unjustified couplings.** A plan shouldn't make a component depend on another, or know about its internals, without a clear reason. Flag it as the `specs` skill does, including when two sub-systems seem to have to change in the same step.
- **Outline, don't code.** Say what changes in which component, and how it's verified. No code, no diffs, no line-by-line instructions.
- **The spec and the test plan are the source of truth.** Don't edit them without the user's go-ahead. When planning reveals a gap or a contradiction, raise it, and propose updating the document (with the `specs` or `tests` skill for one it wrote) so that all three stay consistent.

## Test plan

A test plan is associated with the spec when one of these applies:

- the user gives it;
- it's a document with `type: Test plan` whose `links` or `sources` point to the spec, usually next to the spec as `<spec-id>-test-plan.md`;
- the spec's folder, its `index.md` or the code's test folders otherwise show one exists.

Look for it before planning, and confirm what you find with the user. When there are several candidates, ask which one applies.

**When there is a test plan, the plan's constraint is that the tests pass:**

- **Every test belongs to one step:** the first step after which it must pass. That's the step that writes it, unless it already exists as code. Assign each test to the earliest step that can make it pass: lower-level tests usually come early, and an acceptance test comes with the last step its journey needs.
- **Passing tests stay passing.** After a step, its own tests and those of every previous step pass. Tests assigned to later steps may still fail or be missing, but they never block a step.
- **The whole test plan passes at the end.** After the last step, every test in it passes, and the regression suites it lists have passed. If they touch a step's sub-systems, they also run in that step.
- **The tests don't bend to the plan.** Never drop, weaken or postpone a test past the end of the plan to make the plan work. A test that can't pass (it contradicts the spec, or it's impossible under the plan's Approach) is raised with the user, and the test plan is updated with their go-ahead.
- **Blocked tests stay visible.** A test marked **Blocked** in the test plan is assigned to its step, which is marked **Blocked** too if it can't be completed without the answer. The final reply lists them.
- **A step without a test is a finding.** Either it isn't a deliverable (reshape it), or the test plan has a gap. For a gap, propose the missing test to the user: on their go-ahead, add it with the `tests` skill. A step that needs a check only while the work is under way (e.g. a temporary compatibility path) can state it under Verification, without changing the test plan.
- **Test plan out of step with the spec.** When the test plan is older than the spec's last change, or it covers spec items that no longer exist, tell the user, and ask whether to update it first.

**When there is no test plan,** tell the user, and ask whether to give its path, write it first with the `tests` skill, or go on without. Without one, each step's Verification names the checks that prove it, derived from the spec's acceptance criteria, edge cases and constraints. They don't amount to a test plan: don't design a full test suite here.

## Sub-systems

A sub-system is a part of the system that can be understood, changed and tested on its own, with a boundary other parts go through: typically an app, a service, a shared package, a database schema, a job runner or an infrastructure stack. A repository in a several-repositories scope is at least one sub-system.

Draw the list from the code and the spec's Blast radius, naming each as in the code, and at a grain that fits the system: a monolith's sub-systems are its major modules. Confirm the list with the user before slicing, because the "one or two sub-systems" rule depends on it. Don't redraw it to make a step look smaller.

## Prerequisites

A step's only dependencies are previous steps, so anything else the work needs has to be handled up front. For each of the spec's prerequisites, and any you discover, decide which of these applies:

- **Already true, or must be true before work starts:** list it under Prerequisites, as a condition on step 1. Check the ones you can (a dependency in the code, a merged spec, access to a repository), and tell the user about any that isn't met.
- **Needed later, or something the team has to do:** make it a step of its own, placed before the first step that needs it. Its deliverable is the thing being in place and checked (e.g. "the queue exists in staging and accepts a test message").
- **Not in the team's hands and not yet available** (e.g. another team's API): raise it with the user. It's either a condition on step 1, or the plan stops short of what needs it, and that's stated in the final reply.

## Slicing the work

Start from the spec's user stories and rollout phases, and from what has to exist before what. Useful ways to slice:

- **The thinnest working path first,** then one step for each further behaviour, rule or edge case worth its own review.
- **Inside out across a boundary:** the provider first (data, API, event), tested at its boundary; then each consumer.
- **Expand, migrate, contract** for a shared contract or data with several consumers: add the new form alongside the old, move the consumers one or two sub-systems per step, then remove the old form in its own step. This avoids a step that changes everything at once.
- **Rollout phases:** a phase's steps come before its exit criteria can be checked, and the phases stay in the spec's order. Each phase is reached by the end of a step, never in the middle of one.

Type-specific emphasis:

- **Feature:** a first step that delivers a small, real slice of the outcome, then steps that widen it. Leave no step that only prepares for a later one.
- **Bug fix:** often a single step, which writes the reproducing test (seen to fail first) and makes the fix, as they belong together. Further steps only for a wider fix, such as the same fault in another sub-system.
- **Refactoring:** a first step that writes the characterisation tests and sees them pass against the current code, then steps that each move one thing while those tests stay green, with old code removed last. Every step's deliverable is the same behaviour on the new structure. No step adds a feature.

## AI-XF format

The plan uses the same document format as the specs: an [AI-XF 0.5](https://github.com/DavidROliverBA/ai-xf-format/blob/main/SPEC.md) concept at Level 2 (and therefore an OKF 0.2 concept). Follow the [AI-XF format section of the specs skill](../specs/SKILL.md#ai-xf-format) for frontmatter rules, timestamps, actors, footnotes, links, reserved file names, bundle files and validation, with these differences:

- `type: Implementation plan`.
- `id` and filename stem: `<spec-id>-plan` (e.g. `feature-csv-export-plan`); for a spec without an `id`, use its filename stem.
- `title`: `Implementation plan: <spec title>`, identical to the H1.
- The spec and the test plan are each referenced with a `references` link to their `id` when they're in the same bundle (mirrored by body links in section 1), or with a `sources` entry otherwise.
- `status`: `draft` until its pull request is merged, then `stable`, with `verified` entries added on merge, exactly as for a spec (see [Publishing in the specs skill](../specs/SKILL.md#publishing)). When the spec or the test plan changes after the plan was approved, the plan goes back to `draft` until it's updated.

## Process

### 1. Load the inputs

1. Get the spec from the user, read the whole file, and read the local material it links to. If it's missing or unsuitable, offer to go through the `specs` skill first (see [Rules](#rules)).
2. Identify its format, type and status. If it isn't approved yet (`status: draft`, its pull request not merged), tell the user the plan may need rework, and ask whether to go ahead.
3. Find the [test plan](#test-plan) and read it, with the same check of its status. List its tests, including **Blocked** ones and the regression suites.
4. Settle the codebase scope (see [Rules](#rules)), and check that every repository in it is reachable.
5. If the plan already exists, read it and update it rather than starting over. Keep the numbers of steps that are done, and add new steps after them.

### 2. Explore

If working in a codebase, explore every repository in the codebase scope (a code index such as CodeGraph first, when you have one and the repository is indexed):

- how the components in the Blast radius work today, and what depends on them;
- the patterns to follow, and any existing code to reuse;
- which tests from the test plan already exist as code, and whether they pass today, if that's cheap to find out;
- what the build, the test commands and the delivery process require, since each step has to get through them.

Treat findings as hypotheses to confirm, not facts.

### 3. Settle the approach

Propose, and get confirmation for:

- the [sub-systems](#sub-systems);
- the technical decisions the spec leaves open, each with a recommendation and its reason;
- how each [prerequisite](#prerequisites) is handled.

### 4. Slice into steps

1. Draft the steps (see [Slicing the work](#slicing-the-work)), in the order they'd be delivered. Numbers are the order: a step depends only on lower numbers.
2. For each step, write its deliverable first, as a sentence stating what works afterwards. If you can't, the step isn't one yet.
3. Assign each test in the test plan to a step (see [Test plan](#test-plan)), or derive the step's verification from the spec when there's none.
4. Map every spec item to the step that delivers it, using the same references as the test plan when there is one, or else the [conventions of the tests skill](../tests/SKILL.md#2-inventory).

### 5. Check the steps

Go through every step and answer each question. Reshape the step on any "no", then check the steps that follow it again:

| Rule | Question |
| --- | --- |
| Deliverable | Can you state what works after this step, and is there a test or check that fails without it? |
| Dependencies | Would the step's verification pass with only the previous steps done, and nothing outside the plan beyond the Prerequisites? |
| Scope | Does it touch at most two sub-systems, or is an exception agreed and recorded? Can one pull request hold it? |
| Working system | Can it be merged and shipped on its own, with everything that passed before still passing? |

Then present the overview (the table in section 5 of the [template](template.md), and any exceptions) to the user, and agree on the slicing before writing the details.

### 6. Review with the user

1. Confirm the file location with the user: propose the spec's folder (the same AI-XF bundle), next to the spec, named `<spec-id>-plan.md`, or follow an existing convention in the project.
2. Write the file with `status: draft`, following [template.md](template.md) exactly: same frontmatter, same sections, same order. Then walk the user through it, starting with the overview, and ask for corrections.
3. Iterate until the user says the draft is ready for review. The plan stays `status: draft`: approving its content is the reviewers' job, in the pull request.
4. Update the bundle's `index.md` and `log.md` if they exist, and run the validator if available (see the specs skill).
5. Publish the draft as a pull request, as described in [Publishing in the specs skill](../specs/SKILL.md#publishing), with a branch named after the plan (e.g. `docs/<spec-id>-plan`). When the pull request of the spec or the test plan is still open, ask whether to add the plan to it or to open a separate one.
6. In your final reply, give the pull request's URL, and list:
   - the open questions left in the plan, and the steps and tests they block;
   - the gaps found in the spec or the test plan, and whether they were updated;
   - any exceptions to the step rules, with their reasons;
   - any side issues you noticed.

## Quality checklist

Before presenting the draft, check that:

- [ ] The plan covers exactly one spec, given by the user, and stays inside its Boundaries.
- [ ] Every step states a deliverable: a functionality that works afterwards, and a test or check that shows it.
- [ ] No step is purely preparatory: nothing that only a later step makes useful or testable.
- [ ] Every step depends only on lower-numbered steps; nothing else is needed but the Prerequisites.
- [ ] Every step touches at most two sub-systems, named as in section 3; any exception was agreed and has its reason recorded.
- [ ] Every step leaves the system working and can be merged on its own.
- [ ] Every spec item is delivered by a step, marked **Blocked**, or marked **Outside the plan** with a reason the user agrees with.
- [ ] With a test plan: every test is assigned to exactly one step, passing tests stay passing, no test is dropped or weakened, and the last step ends with the whole test plan passing.
- [ ] Without a test plan: the user knows, and every step's Verification is traced to the spec.
- [ ] For a bug fix: the reproducing test is seen to fail, then passes, in the same step as the fix.
- [ ] For a refactoring: characterisation tests pass before the first structural change, and no step adds a feature.
- [ ] Rollout phases, if any, are in the spec's order, and each one ends at the end of a step.
- [ ] Technical decisions are recorded under Approach, with their reasons, and validated by the user.
- [ ] Every component is named as in the code, or marked as new; nothing is invented.
- [ ] The codebase scope matches the spec's, is confirmed by the user, and is recorded with the branch and commit analysed per repository.
- [ ] No code, diffs or line-by-line instructions.
- [ ] The plan is `status: draft`, with no new `verified` entry: only the merge of its pull request makes it `stable`.
- [ ] AI-XF Level 2: same checks as the specs skill, with `type: Implementation plan` and a resolvable link or source to the spec and the test plan.

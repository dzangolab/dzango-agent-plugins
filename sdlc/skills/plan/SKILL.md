---
name: plan
description: Turn a given technical spec (a single markdown file, such as one written by the specs skill) into an implementation plan, without code — hard technical problems first, strong technical choices with their options and ADRs, then phases of small steps that several developers can work on independently, each step producing a testable deliverable and depending only on previous steps. When a test plan exists for the spec, the plan's constraint is that its tests pass. Use when the user asks for an implementation plan, a build plan, or the steps to implement a spec, or invokes the plan skill directly (e.g. /sdlc:plan in Claude Code).
---

# Plan

Produce one implementation plan document for one spec: the phases and steps that take the system from where it is today to the spec being delivered. The plan is the input for the next SDLC step, implementation.

A good plan:

- **Faces the hard problems first.** What might not work, or isn't known yet, is settled before anything is built on it (see [Hard problems](#hard-problems)).
- **Makes its strong technical choices explicitly,** with the options side by side (see [Technical choices](#technical-choices)).
- **Can be dispatched.** Several developers can pick up work and proceed independently of each other, because the sub-systems are as decoupled as they can be (see [Independence](#independence)).
- **Advances by real outcomes.** Each phase is significant progress, and each step is a small piece of working functionality (see [Phases](#phases) and [Step rules](#step-rules)).

The plan outlines the work. It contains no code.

## Step rules

Every step must meet all of these. Check each step against them (see [Check the plan](#6-check-the-plan)) and reshape any step that fails.

- **It produces a deliverable: a testable functionality.** When the step is done, something works that didn't before (or, in a refactoring, the same behaviour rests on the new structure), and a test can show it. "Create the table", "add the types" or "write the helper" aren't deliverables on their own: combine them with the behaviour they enable. The functionality doesn't have to reach an end user: it can be observed at the boundary of the sub-system that delivers it (an endpoint answering, a job producing its output, a component rendering its states). There are two special cases: a [feasibility step](#hard-problems) delivers an answer, shown by a run anyone can repeat, and a [contract step](#independence) delivers a contract that consumers can test against.
- **It depends only on previous steps.** A step relies on nothing but the state of the system before step 1, plus the steps before it. It never needs a later step to compile, pass its tests or work, and it relies on no work outside the plan: see [Prerequisites](#prerequisites). Step numbers give a valid order, not a required sequence: list only the steps a step really needs, so that steps with no dependency between them can be worked on at the same time.
- **It has a limited scope.** It touches one sub-system by preference, and two at most (see [Sub-systems](#sub-systems)), and it's small enough to review as one pull request. When a step needs more, first try another slice (see [Slicing the work](#slicing-the-work)). If it truly can't be split, keep it only with the user's agreement, and record the reason in the step.
- **It leaves the system working.** Everything that was passing before the step still passes after it, and the step can be merged and shipped on its own. Functionality that isn't ready for users stays hidden, by a means the spec or the user has agreed (e.g. a feature flag), not by leaving things broken.

Prefer slicing by functionality to slicing by layer: "export one report as CSV, through the API" is a step; "all the database work" isn't. When one piece of functionality crosses more than two sub-systems, slice it at a sub-system boundary instead, with the deliverable tested at that boundary: first the API endpoint, then the UI that calls it.

## Rules

- **The spec must be given.** The input is one spec, given as a path to (or the content of) a single markdown file. Without one, ask for it; never build a plan from the conversation, an issue or the code alone. If the spec spans several files or isn't markdown, say so and ask the user for a single markdown file.
- **Assume a well-formed spec, and check that it's suitable.** A spec is usually written with the `specs` skill and needs no interview. It's unsuitable when it isn't a spec, it mixes several subjects or types, it doesn't state the expected outcome, or too much of what the steps need is missing. In that case, or when no spec is available, explain why in a few words and offer to go through the `specs` skill first, using the supplied material as its input. If the user accepts, run the `specs` skill, then plan from the spec it produces. If the user declines, go on only if the spec has enough to plan from; otherwise stop.
- **Any spec format.** A spec written by the `specs` skill (see [its template](../specs/template.md)) maps directly onto the plan. For any other spec, map its content onto the same concepts (intent, type, constraints, boundaries, edge cases, user stories or requirements, rollout phases, prerequisites, blast radius) and confirm the mapping with the user before planning.
- **A test plan is the plan's constraint.** See [Test plan](#test-plan).
- **Same codebase scope as the spec.** Plan the work in the repositories the spec's codebase scope covers. Take the scope from the spec (a spec written by the `specs` skill records it in Blast radius) and confirm it with the user; when the spec doesn't state it, settle it as described in the [codebase scope section of the specs skill](../specs/SKILL.md#codebase-scope), which also covers repositories without a local working copy. Work needed in a repository outside the scope is raised with the user, never added silently.
- **One plan per spec.** The plan implements exactly the spec's subject and respects its Boundaries. Anything else (a bug you noticed, a clean-up you'd like to do) is a side issue: tell the user and note it in your final reply; don't add steps for it. Preparatory work belongs in the plan only when a step needs it.
- **Don't invent, don't assume.** Every component in the plan is named as in the code, or marked as new. When the spec is silent on something a step needs, ask, one question at a time by preference and never more than 3 at a time, using your structured question tool (e.g. `AskUserQuestion` in Claude Code) when the answer is a choice between concrete options. If the user explicitly defers a question, write it inline as `> **Open question:** …` next to the steps it blocks, and mark those steps **Blocked**; never fill it with a guess.
- **Outline, don't code.** Say what changes in which component, and how it's verified. No code, no diffs, no line-by-line instructions.
- **The spec and the test plan are the source of truth.** Don't edit them without the user's go-ahead. When planning reveals a gap or a contradiction, raise it, and propose updating the document (with the `specs` or `tests` skill for one it wrote) so that all three stay consistent.

## Hard problems

Start here: before slicing anything, go through the spec looking for its hard technical problem areas, and above all for those whose feasibility is uncertain. Typical signs:

- nobody on the team has done it before, or it uses a technology new to the codebase;
- a constraint that may not be reachable (a performance or scale target, offline use, a browser or platform limit);
- behaviour that depends on a third party's API, limits, pricing or terms, and hasn't been tried;
- concurrency, consistency, ordering or real-time behaviour;
- a data migration, or a change that can't be rolled back;
- a security, privacy or compliance requirement whose solution isn't obvious;
- a question where the answer would change the design, or the spec itself.

List each one with what's uncertain and which spec items it affects, and confirm the list with the user, who often knows of others. If you find none, say so in the plan: that's a useful statement for reviewers to challenge.

**Then settle each one as early as possible,** by the cheapest means that gives a real answer:

1. **While planning,** when reading the code, the documentation or a vendor's limits is enough. Record the answer and its source. Building and running anything for this needs the user's go-ahead.
2. **As the first steps of the plan,** when something has to be built to find out:
   - **A thin real slice,** when the functionality itself can answer the question. It's an ordinary step, placed first.
   - **A feasibility step,** otherwise, in a Phase 0 that comes before all other phases (see [Phases](#phases)), including when the problem concerns a later phase of the spec: the answer may change what comes before it.

A feasibility step states:

- the question, in one sentence;
- how it's tested, on what data or at what load, and the pass or fail result it must give, using the spec's thresholds when it has them;
- a limit on the effort, as agreed with the user, after which the question is treated as unanswered;
- what happens on failure: the fallback option, or a return to the user to change the approach or the spec.

Its deliverable is the answer, with a run that can be repeated. Its code is a throwaway unless the user decides otherwise, so it isn't merged into the product and carries no tests from the test plan.

Any step whose design rests on the answer lists the feasibility step under **Depends on**. Those steps are provisional: when the answer is known, the plan is reviewed and updated before they start.

## Technical choices

The spec states what, so this is where the how gets decided. Most choices are routine: follow the codebase's existing patterns, and don't list them.

Point out the areas that need a **strong choice**: one that's costly to reverse, that shapes several sub-systems or their contracts, that adds a technology or a dependency, or that has lasting consequences on cost, security or operations. For each:

1. Propose **at least two real technical solutions**, each one workable: don't add a straw man to make up the number. If you can only find one, say why it has no credible alternative, and ask the user whether they see one.
2. Give the trade-offs of each, against the spec's constraints and the coupling it would create (see [Independence](#independence)).
3. Recommend one, and let the user decide. When the decision rests on a hard problem not yet settled, say which step settles it, and name the option the plan assumes meanwhile.

Record every strong choice under Technical choices in the plan.

**Create an ADR (architecture decision record) where appropriate:** when the choice will outlive this plan, is hard to reverse, or affects work beyond this spec, or when the team will one day ask why it was made. A local choice that the plan's table explains well enough doesn't need one.

- **Location and format.** Look for an existing ADR convention in the codebase scope (a folder such as `docs/adr/`, `adr/` or `docs/decisions/`, or the project's agent instructions), and follow its location, numbering and format. Without one, propose an `adr/` folder in the plan's AI-XF bundle, and confirm it with the user.
- **Template.** Without an existing format, use [adr-template.md](adr-template.md): one file per decision, `adr-<slug>.md`, which is an AI-XF concept like the plan.
- **Lifecycle.** An ADR is published with the plan, in the same pull request, as `status: draft`, and it's accepted by that pull request's review and merge, like the plan. A decision left to a feasibility step gets its ADR in that step.
- **Links.** The plan has a `references` link to each ADR in the bundle, mirrored by a body link in Technical choices.

## Sub-systems

A sub-system is a part of the system that can be understood, changed and tested on its own, with a boundary other parts go through: typically an app, a service, a shared package, a database schema, a job runner or an infrastructure stack. A repository in a several-repositories scope is at least one sub-system.

Draw the list from the code and the spec's Blast radius, naming each as in the code, and at a grain that fits the system: a monolith's sub-systems are its major modules. Confirm the list with the user before slicing, because the step rules and the independence analysis depend on it. Don't redraw it to make a step look smaller.

## Independence

A good plan lets the work be dispatched among several developers, each working independently of the others. Evaluate this explicitly, and shape the plan around it.

**Evaluate each pair of impacted sub-systems.** Does work on one need the other, and why? Classify the pair:

| Relation | Meaning | What the plan does |
| --- | --- | --- |
| Independent | Neither needs the other. | Separate steps, with no dependency between them. |
| Through a contract | One uses the other's API, events or schema, and nothing more. | A [contract step](#contract-steps), so that both can proceed at the same time. |
| Sequenced | One can only start once the other's work is done, even with a contract. | A dependency between their steps, with the reason recorded. |

**Avoid unnecessary coupling.** Each dependency between sub-systems must have a reason, both in the design and in the work. Typical signs of an unjustified one are those listed in the `specs` skill: a generic or shared package that knows about a specific consumer, a component reaching into another's internals instead of its public API, two components that must change or ship together, or a feature that only works with one implementation of a dependency. Also look for a step that touches two sub-systems when it could be two steps, a shared file or table that several workstreams would edit, and a decision that one developer can't take without another. When you find one, propose a decoupled approach. If the user justifies the coupling instead, record the reason.

**Above all, avoid cycles,** where two sub-systems depend on each other. Numbered steps can't form a cycle, but sub-systems can: step 1 in A, step 2 in B needing step 1, step 3 in A needing step 2. Developers on A and B then wait for each other in turn, and neither stream can go ahead alone. Check two graphs, and draw them if it helps:

- **In the work:** an arrow from sub-system A to B when a step in A depends on a step in B.
- **In the design:** an arrow from A to B when A's code calls, imports or reads B once the spec is delivered.

Break a cycle by defining a contract owned by one side, inverting one dependency (e.g. the other side publishes an event, or receives a callback), extracting what both need into a third component, or reordering the steps so that one sub-system's work finishes first. A cycle that remains is a finding: flag it, and keep it only with the user's agreement and a recorded reason.

**State the result honestly.** The plan's Independence section gives the relation of each pair, the workstreams (each one what a single developer could take on), what each waits for, and where they have to meet. Where work can't be made independent, say so and why, rather than presenting a sequence as parallel work. A small change with one developer's worth of work is a fine result: don't add contract steps or workstreams it doesn't need.

### Contract steps

When a consumer would otherwise wait for its provider, a contract step defines what passes between them, for the provider's owner and the consumer's owner to agree on: an API description, an event or schema definition, with a stub or example data that the consumer can run against, and checks that can fail (e.g. contract tests or schema validation). Both sides then depend on the contract step, and not on each other. A later step, which depends on both, brings them together, and the tests that need both real sides belong to it.

The contract belongs to the sub-system that provides it, and the consumer uses it from there. It adds no shared code that couples them beyond the contract.

## Phases

Phases group the steps into stages of significant progress. A plan with one obvious outcome doesn't need them.

- **The spec's phases must be honoured.** When the spec defines several phases (the Rollout section of a spec written by the `specs` skill), each of them is a phase of the plan, with the same number and name, in the same order, delivering the same scope, to the same audience, and meeting the same exit criteria. Never move an item between them, merge them or reorder them. Their order is the order of delivery, so work on a later phase can start earlier when its steps allow it, as long as its release waits for the earlier phase. If the work suggests a different split, or an earlier phase seems to need something from a later one, raise it with the user, and change the spec with their go-ahead.
- **The plan can add its own.** When the spec defines no phases, the plan can define some. Within a spec's phase, it can define sub-phases (`Phase 2.1`, `Phase 2.2`). These are stages of the work only, and don't add a rollout to users.
- **Each phase has an outcome that's significant progress in the implementation:** ideally, the implementation of the spec within one sub-system. If the spec requires an API to be created or changed, the finished API is a significant outcome, and can be its own phase. "Backend part 1", or a batch of steps that happen to be close in time, isn't one.
- **The outcome can be verified.** It says what's true at the end of the phase, and the tests or checks that show it. For a spec's phase, its exit criteria are part of this; one that can only be measured after release, in production, is marked as such.
- **Phases contain whole steps.** A phase ends at the end of a step. Step numbers run through the whole plan, so that "lower numbers only" keeps working.
- **Phases follow the same rules as steps.** A phase depends only on earlier phases, with its dependencies listed. Phases with no dependency between them, such as two sub-systems working from the same contract, can run at the same time.
- **Phase 0 is for feasibility.** It holds the [feasibility steps](#hard-problems), when there are any, and doesn't change the numbering of the spec's phases. It delivers nothing to users, so it doesn't alter the rollout.

## Prerequisites

A step's only dependencies are previous steps, so anything else the work needs has to be handled up front. For each of the spec's prerequisites, and any you discover, decide which of these applies:

- **Already true, or must be true before work starts:** list it under Prerequisites, as a condition on step 1. Check the ones you can (a dependency in the code, a merged spec, access to a repository), and tell the user about any that isn't met.
- **Needed later, or something the team has to do:** make it a step of its own, placed before the first step that needs it, and in the right phase for a prerequisite the spec ties to a phase. Its deliverable is the thing being in place and checked (e.g. "the queue exists in staging and accepts a test message").
- **Not in the team's hands and not yet available** (e.g. another team's API): raise it with the user. It's either a condition on step 1, or the plan stops short of what needs it, and that's stated in the final reply.

## Test plan

A test plan is associated with the spec when one of these applies:

- the user gives it;
- it's a document with `type: Test plan` whose `links` or `sources` point to the spec, usually next to the spec as `<spec-id>-test-plan.md`;
- the spec's folder, its `index.md` or the code's test folders otherwise show one exists.

Look for it before planning, and confirm what you find with the user. When there are several candidates, ask which one applies.

**When there is a test plan, the plan's constraint is that the tests pass:**

- **Every test belongs to one step:** the first step after which it must pass. That's the step that writes it, unless it already exists as code. Assign each test to the earliest step that can make it pass: lower-level tests usually come early, and an acceptance test comes with the last step its journey needs.
- **Passing tests stay passing.** After a step, its own tests and those of every step it depends on pass, and, once the steps are merged together, those of every merged step. Tests assigned to later steps may still fail or be missing, but they never block a step.
- **The whole test plan passes at the end.** After the last step, every test in it passes, and the regression suites it lists have passed. If they touch a step's sub-systems, they also run in that step.
- **The tests don't bend to the plan.** Never drop, weaken or postpone a test past the end of the plan to make the plan work. A test that can't pass (it contradicts the spec, or it's impossible under the plan's technical choices) is raised with the user, and the test plan is updated with their go-ahead.
- **Blocked tests stay visible.** A test marked **Blocked** in the test plan is assigned to its step, which is marked **Blocked** too if it can't be completed without the answer. The final reply lists them.
- **A step without a test is a finding,** except for a feasibility step, which has its pass or fail result instead. Either it isn't a deliverable (reshape it), or the test plan has a gap. For a gap, propose the missing test to the user: on their go-ahead, add it with the `tests` skill. A step that needs a check only while the work is under way (e.g. a temporary compatibility path) can state it under Verification, without changing the test plan.
- **Test plan out of step with the spec.** When the test plan is older than the spec's last change, or it covers spec items that no longer exist, tell the user, and ask whether to update it first.

**When there is no test plan,** tell the user, and ask whether to give its path, write it first with the `tests` skill, or go on without. Without one, each step's Verification names the checks that prove it, derived from the spec's acceptance criteria, edge cases and constraints. They don't amount to a test plan: don't design a full test suite here.

## Slicing the work

Start from the settled hard problems, the spec's phases and user stories, and what has to exist before what. Useful ways to slice:

- **The thinnest working path first,** then one step for each further behaviour, rule or edge case worth its own review.
- **Sub-system by sub-system, from a contract:** the contract first, then the provider and each consumer as separate streams, then a step that brings them together.
- **Inside out across a boundary:** the provider first, tested at its boundary, then each consumer. Use it when a contract step isn't worth it, because the work is small or has one owner.
- **Expand, migrate, contract** for a shared contract or data with several consumers: add the new form alongside the old, move the consumers one or two sub-systems per step, then remove the old form in its own step. This avoids a step that changes everything at once.

Type-specific emphasis:

- **Feature:** a first step that delivers a small, real slice of the outcome (or the feasibility answers it needs), then steps that widen it. Apart from feasibility and contract steps, leave no step that only prepares for a later one.
- **Bug fix:** often a single step, which writes the reproducing test (seen to fail first) and makes the fix, as they belong together. Further steps only for a wider fix, such as the same fault in another sub-system. Phases, workstreams and ADRs are rarely needed.
- **Refactoring:** a first step that writes the characterisation tests and sees them pass against the current code, then steps that each move one thing while those tests stay green, with old code removed last. Every step's deliverable is the same behaviour on the new structure. No step adds a feature. Reducing coupling is often the point: state which couplings go away.

## AI-XF format

The plan uses the same document format as the specs: an [AI-XF 0.5](https://github.com/DavidROliverBA/ai-xf-format/blob/main/SPEC.md) concept at Level 2 (and therefore an OKF 0.2 concept). Follow the [AI-XF format section of the specs skill](../specs/SKILL.md#ai-xf-format) for frontmatter rules, timestamps, actors, footnotes, links, reserved file names, bundle files and validation, with these differences:

- `type: Implementation plan`.
- `id` and filename stem: `<spec-id>-plan` (e.g. `feature-csv-export-plan`); for a spec without an `id`, use its filename stem.
- `title`: `Implementation plan: <spec title>`, identical to the H1.
- The spec, the test plan and each ADR are referenced with a `references` link to their `id` when they're in the same bundle (mirrored by body links), or with a `sources` entry otherwise.
- `status`: `draft` until its pull request is merged, then `stable`, with `verified` entries added on merge, exactly as for a spec (see [Publishing in the specs skill](../specs/SKILL.md#publishing)). When the spec or the test plan changes after the plan was approved, the plan goes back to `draft` until it's updated.

## Process

### 1. Load the inputs

1. Get the spec from the user, read the whole file, and read the local material it links to. If it's missing or unsuitable, offer to go through the `specs` skill first (see [Rules](#rules)).
2. Identify its format, type, status and phases. If it isn't approved yet (`status: draft`, its pull request not merged), tell the user the plan may need rework, and ask whether to go ahead.
3. Find the [test plan](#test-plan) and read it, with the same check of its status. List its tests, including **Blocked** ones and the regression suites.
4. Settle the codebase scope (see [Rules](#rules)), and check that every repository in it is reachable.
5. If the plan already exists, read it and update it rather than starting over. Keep the numbers of steps that are done, and add new steps after them.

### 2. Find the hard problems

1. Read the spec for its [hard problems](#hard-problems) before looking at solutions, and list them.
2. If working in a codebase, explore every repository in the codebase scope (a code index such as CodeGraph first, when you have one and the repository is indexed), starting with the hard problems, and then:
   - how the components in the Blast radius work today, and what depends on them;
   - the patterns to follow, and any existing code to reuse;
   - which tests from the test plan already exist as code, and whether they pass today, if that's cheap to find out;
   - what the build, the test commands and the delivery process require, since each step has to get through them.

   Treat findings as hypotheses to confirm, not facts.
3. Confirm the list with the user, along with how each problem gets settled.

### 3. Settle the approach

Propose, and get confirmation for:

- the [sub-systems](#sub-systems);
- the [strong technical choices](#technical-choices), each with at least two solutions and a recommendation, and which ones get an ADR;
- how each [prerequisite](#prerequisites) is handled.

### 4. Evaluate independence

Classify each pair of sub-systems, look for unnecessary coupling and cycles (see [Independence](#independence)), and agree with the user on how to resolve them. Do this before slicing: it decides where the contract steps go, and may change a technical choice.

### 5. Slice into phases and steps

1. Lay out the [phases](#phases): Phase 0 if there are feasibility steps, then the spec's phases, then any phases or sub-phases of the plan's own, each with its outcome.
2. Draft the steps of each phase (see [Slicing the work](#slicing-the-work)) in a valid order. Numbers run through the whole plan, and a step depends only on lower numbers.
3. For each step, write its deliverable first, as a sentence stating what works afterwards. If you can't, the step isn't one yet.
4. Assign each test in the test plan to a step (see [Test plan](#test-plan)), or derive the step's verification from the spec when there's none.
5. Map every spec item to the step that delivers it, using the same references as the test plan when there is one, or else the [conventions of the tests skill](../tests/SKILL.md#2-inventory).
6. Group the steps into workstreams, and note what each waits for.

### 6. Check the plan

Go through every step and answer each question. Reshape the step on any "no", then check the steps that follow it again:

| Rule | Question |
| --- | --- |
| Deliverable | Can you state what works after this step, and is there a test or check that fails without it? |
| Dependencies | Would the step's verification pass with only the steps it lists done, and nothing outside the plan beyond the Prerequisites? |
| Scope | Does it touch at most two sub-systems, or is an exception agreed and recorded? Can one pull request hold it? |
| Working system | Can it be merged and shipped on its own, with everything that passed before still passing? |

Then look at the plan as a whole:

| Topic | Question |
| --- | --- |
| Hard problems | Is each one settled during planning, or by a step that comes before all the work resting on it? |
| Phases | Are the spec's phases all there, unchanged? Does every phase have a verifiable outcome that's significant progress? |
| Independence | Could two developers start at the same time wherever the plan says so, without waiting for each other? Is every remaining dependency between sub-systems justified? |
| Cycles | Are the work and design graphs free of cycles, or is each remaining one agreed and recorded? |

Then present the overview (section 6 of the [template](template.md)), the independence analysis (section 7) and any exceptions to the user, and agree on the slicing before writing the details.

### 7. Review with the user

1. Confirm the file location with the user: propose the spec's folder (the same AI-XF bundle), next to the spec, named `<spec-id>-plan.md`, or follow an existing convention in the project. Do the same for the ADRs (see [Technical choices](#technical-choices)).
2. Write the file with `status: draft`, following [template.md](template.md) exactly: same frontmatter, same sections, same order. Write the ADRs the same way. Then walk the user through it, starting with the hard problems and the overview, and ask for corrections.
3. Iterate until the user says the draft is ready for review. The plan stays `status: draft`: approving its content is the reviewers' job, in the pull request.
4. Update the bundle's `index.md` and `log.md` if they exist, and run the validator if available (see the specs skill).
5. Publish the draft and its ADRs as a pull request, as described in [Publishing in the specs skill](../specs/SKILL.md#publishing), with a branch named after the plan (e.g. `docs/<spec-id>-plan`). When the pull request of the spec or the test plan is still open, ask whether to add the plan to it or to open a separate one.
6. In your final reply, give the pull request's URL, and list:
   - the hard problems still open, and the steps that settle them;
   - the strong choices and their ADRs;
   - any remaining cycles, and any exceptions to the step rules, with their reasons;
   - the open questions left in the plan, and the steps and tests they block;
   - the gaps found in the spec or the test plan, and whether they were updated;
   - any side issues you noticed.

## Quality checklist

Before presenting the draft, check that:

- [ ] The plan covers exactly one spec, given by the user, and stays inside its Boundaries.
- [ ] Hard technical problems are listed, or the plan says there are none; each is settled during planning or by a step that comes before all the work resting on it.
- [ ] Every feasibility step has a question, a pass or fail result, a limit on the effort and a fallback, and steps resting on it depend on it.
- [ ] Every strong choice has at least two workable solutions with their trade-offs (or the reason there's no credible alternative), a recommendation, and the user's decision or the step that settles it.
- [ ] ADRs exist where a choice warrants one, follow the project's convention when there is one, and are linked from the plan.
- [ ] Every phase of the spec is a phase of the plan, with the same number, order, scope and exit criteria.
- [ ] Every phase has a verifiable outcome that's significant progress, and contains whole steps.
- [ ] Every pair of impacted sub-systems is classified, and each dependency between sub-systems has a reason.
- [ ] No cycle between sub-systems, in the work or the design, without the user's agreement and a recorded reason.
- [ ] Workstreams show who could work independently, what each waits for, and where they meet; nothing sequenced is presented as parallel.
- [ ] Every step states a deliverable: a functionality that works afterwards, and a test or check that shows it.
- [ ] No step is purely preparatory, apart from feasibility and contract steps.
- [ ] Every step depends only on lower-numbered steps, lists only those it really needs, and needs nothing else but the Prerequisites.
- [ ] Every step touches at most two sub-systems, named as in section 4; any exception was agreed and has its reason recorded.
- [ ] Every step leaves the system working and can be merged on its own.
- [ ] Every spec item is delivered by a step, marked **Blocked**, or marked **Outside the plan** with a reason the user agrees with.
- [ ] With a test plan: every test is assigned to exactly one step, passing tests stay passing, no test is dropped or weakened, and the last step ends with the whole test plan passing.
- [ ] Without a test plan: the user knows, and every step's Verification is traced to the spec.
- [ ] For a bug fix: the reproducing test is seen to fail, then passes, in the same step as the fix.
- [ ] For a refactoring: characterisation tests pass before the first structural change, and no step adds a feature.
- [ ] Every component is named as in the code, or marked as new; nothing is invented.
- [ ] The codebase scope matches the spec's, is confirmed by the user, and is recorded with the branch and commit analysed per repository.
- [ ] No code, diffs or line-by-line instructions.
- [ ] The plan and its ADRs are `status: draft`, with no new `verified` entry: only the merge of their pull request makes them `stable`.
- [ ] AI-XF Level 2: same checks as the specs skill, with `type: Implementation plan` and a resolvable link or source to the spec, the test plan and the ADRs.

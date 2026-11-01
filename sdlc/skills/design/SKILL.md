---
name: design
description: Propose a system design for a given technical spec (a single markdown file, such as one written by the specs skill) as an architecture document made mainly of C4 diagrams in Mermaid - system context, containers, components where needed, and one dynamic diagram of the workflow between sub-systems for each user story - with words only to clarify the diagrams. Offers to run a spec that is ambiguous, confused or otherwise unusable through the specs skill first. Use when the user asks for a design, an architecture, a system or solution design, C4 diagrams or a technical design for a spec, or invokes the design skill directly (e.g. /sdlc:design in Claude Code).
---

# Design

Propose one design document for one spec: which sub-systems are involved, and how they work together to deliver each user story. The spec says what; the design says how, at the level of the system's architecture. The design is an input for the next SDLC steps: the test plan, the implementation plan and the implementation.

A good design:

- **Is read from its diagrams.** See [Diagrams first](#diagrams-first).
- **Follows the C4 conventions.** See [C4 conventions](#c4-conventions).
- **Identifies every sub-system involved,** and says which are new, changed or unchanged. See [Sub-systems](#sub-systems).
- **Shows a workflow between the sub-systems for each user story.** See [Workflows](#workflows).

## Diagrams first

The document is made of diagrams. Words clarify the diagrams; diagrams don't illustrate the words.

- **Start from the diagram.** Draw it first, then write only what it can't show. Each diagram comes before any words about it.
- **Words add, they don't repeat.** A note gives what the diagram can't say: why this shape, a rule behind an arrow, an unusual failure, a limit. Never narrate the arrows or restate an element's description.
- **Keep notes short:** three bullets per diagram at most, one or two lines each. When you need more, the diagram is missing something: add it there, or split the diagram.
- **No free-standing prose.** Each section is its diagrams, their notes and, where the template has one, a table. The tables only index and trace what the diagrams show.
- **Check it both ways.** Hide the words: the design can still be followed. Hide the diagrams: the words alone don't make a design.

## Rules

- **The spec must be given.** The input is one spec, given as a path to (or the content of) a single markdown file. Without one, ask for it, or offer to write it with the `specs` skill; never design from the conversation, an issue or the code alone.
- **Check that the spec is usable, then offer `specs`.** See [Usable spec](#usable-spec).
- **One design per spec.** The design covers exactly the spec's subject and respects its Boundaries. Anything else you notice is a side issue: tell the user and note it in your final reply; don't design for it.
- **Propose the design, don't invent the requirements.** The architecture is yours to propose, with a recommendation; the user decides. Requirements aren't: when the design depends on something the spec doesn't say, ask, one question at a time by preference and never more than 3 at a time, using your structured question tool (e.g. `AskUserQuestion` in Claude Code) when the answer is a choice between concrete options. If the user defers it, write it inline as `> **Open question:** …` under the diagram it affects, and show the alternatives in that diagram with an `alt` block or a marked element.
- **Same codebase scope as the spec.** Take the scope from the spec (a spec written by the `specs` skill records it in Blast radius) and confirm it with the user; when the spec doesn't state it, settle it as described in the [codebase scope section of the specs skill](../specs/SKILL.md#codebase-scope). A sub-system found outside the scope is raised with the user, never added silently.
- **Name what exists as in the code.** Every element is named as in the code, or marked as new. Findings from the code are hypotheses to confirm, not facts. Never change a working copy to read it.
- **No plan and no code.** No phases of work, steps, task lists, code, field-level schemas or endpoint listings: the `plan` skill and the implementation decide those. C4's optional code level is left out for the same reason.
- **The spec is the source of truth.** Don't edit it without the user's go-ahead. When designing reveals a gap or a contradiction, raise it, and propose updating the spec with the `specs` skill.

## Usable spec

A spec is usable when a design can be proposed from it without guessing what's wanted.

**A spec written by the `specs` skill is good enough: don't assess it,** ask no further questions about it, and proceed. That includes a spec you've just had `specs` write, and any spec that follows [its template](../specs/template.md). Questions it leaves open are carried into the design as open questions. A `status: draft` spec is still usable: tell the user the design may need rework if the spec changes in review.

Any other input is assessed. It's unusable when it is:

- **Ambiguous:** a statement that decides the architecture can be read in several ways, or rests on vague words ("fast", "scalable", "real-time", "some users") with nothing to measure them by.
- **Confused:** it contradicts itself, mixes several subjects, or mixes the problem with a half-described solution so that the outcome can't be told apart.
- **Otherwise unusable:** it isn't a spec, it doesn't state the expected outcome, it has neither user stories nor requirements that a workflow could be drawn for, or too much of what the design needs is missing.

When it's unusable:

1. Say why in a few words, quoting the passages at fault, and offer to run it through the `specs` skill, using the supplied material as its input.
2. **If the user accepts,** run the `specs` skill to its end, then design from the spec it produces, without assessing it again.
3. **If the user declines,** go on only if a few questions can close the gaps; record what they settle under Scope as a stated assumption. Otherwise stop, and say what's missing.

A usable spec in another format is mapped onto the same concepts (intent, type, constraints, boundaries, edge cases, user stories or requirements, rollout phases, prerequisites, blast radius). When it has requirements but no user stories, group them into the scenarios that will get a workflow. Confirm the mapping with the user before designing.

## C4 conventions

The design follows the [C4 model](https://c4model.com) and its [notation](https://c4model.com/diagrams/notation).

### Diagrams

| C4 diagram | Answers | Scope | In the document |
| --- | --- | --- | --- |
| System context (level 1) | Who and what does the system deal with? | The software system: people and software systems only, no containers. | Always. |
| Container (level 2) | What are the sub-systems, and how do they talk? | The software system: its containers, with the people and systems around them. | Always. For a refactoring, one for today and one for the target. |
| Component (level 3) | What's inside this sub-system? | One container. | Only for a new or changed container whose inside decides how a story works, or for a modular monolith's modules. |
| Dynamic | How do the elements work together at runtime for this story? | One user story. | Always, one per user story (see [Workflows](#workflows)). |
| Deployment | Where does it all run? | One environment. | Only when the spec has hosting, region, availability or isolation constraints, or the design changes how things are deployed. |

The code level (level 4) is never drawn (see [Rules](#rules)). Other Mermaid diagrams (a `stateDiagram-v2` of an entity's lifecycle, an `erDiagram` of main concepts) can complement a C4 diagram, when a workflow depends on them, but don't replace one.

### Notation

Every diagram, whatever its level:

- **Title:** its type and scope, e.g. `Container diagram: Report export`.
- **Key:** explains its colours, shapes and line styles. Structural diagrams have one in the diagram; dynamic diagrams use the document's [Key](template.md) section.
- **Elements:** each has a name, its explicit type (`Person`, `Software system`, `Container`, `Component`, `Deployment node`), a short description of its responsibilities, and, for containers and components, its technology.
- **Relationships:** one-way arrows, labelled with their intent in a verb phrase whose direction matches the arrow; never a bare "Uses". Between containers, the technology or protocol is on the label too, e.g. `Publishes the job [SQS]`.
- **Abbreviations:** those not obvious to every reader are explained in the key.

C4 terms to apply:

- **A container is a runtime boundary,** an application or data store that must be running for the system to work: a server-side app, a client-side app, a serverless function, a database, a bucket, a script. Not a Docker container, and not a code package.
- **Data stores you own are containers,** even when someone else hosts them.
- **Each queue or topic is its own container,** rather than the message bus. To simplify a crowded diagram, you may put the name on the arrow instead (`Sends the job via export-queue [SQS]`).
- **A shared library isn't a container:** it runs inside the containers that use it. Show it, when it matters, as a component.
- **Systems outside the scope** are software systems, in grey, even when they belong to the same organisation.

### Mermaid

Draw with Mermaid, in fenced `mermaid` blocks, so that the diagrams live in the markdown and can be reviewed in a pull request. Use the exact forms in [template.md](template.md):

- **Structural diagrams use `flowchart`,** with the C4 notation written into the nodes and the standard C4 colours. Don't use Mermaid's `C4Context`, `C4Container` and related diagrams: they have no automatic layout, so labels overlap, and they support neither a legend nor tags, so they can't show a key or what's new.
- **Dynamic diagrams use `sequenceDiagram`,** the sequence style that C4 allows for dynamic diagrams. It stays readable for a whole story, including its failure paths.
- **Status is in words and in the border.** New, changed and unchanged are written in the element's type line, so that they don't depend on colour alone.
- **Syntax traps:** always quote flowchart labels; never use `end` as a node ID or alone in a label; keep `;` and `#` out of sequence messages.

Check that every diagram renders. Use a renderer if one is available (e.g. `mmdc` from `@mermaid-js/mermaid-cli`, which also accepts a markdown file); don't install one without the user's go-ahead. When you can't render, say so in your final reply.

## Sub-systems

A sub-system is a part of the system that can be understood, changed and tested on its own, with a boundary other parts go through. In C4 terms it's a **container** by default. Use another level when that's the grain that fits: a **software system** when the spec spans several systems you own, a **component** for a modular monolith's major modules. State the grain once in the document, and use it throughout.

The sub-systems section identifies all of them: the Container diagram, then a table indexing it with each one's status, location and the user stories it takes part in. Responsibility and technology are already in the diagram. "Involved" includes the unchanged ones that a workflow goes through. Draw the list from the spec's Blast radius and the code, and confirm it with the user. The `plan` skill slices its work on the same sub-systems, so use the same names.

**Design for loose coupling.** Each arrow between sub-systems must have a reason. Watch for the signs listed in the [`specs` skill](../specs/SKILL.md#rules) (a shared package that knows about one consumer, a component reaching into another's internals, two components that must ship together), and above all for a **cycle**: following the arrows of the Container diagram from a sub-system back to itself. Break a cycle by having one side own a contract, inverting a dependency (an event, a callback), or extracting what both need. If the user justifies a coupling or a cycle, record the reason in the notes under the diagram.

## Workflows

Draw one dynamic diagram per user story, in the spec's order, named after it (`US-1: Export a report`), so that every story can be followed through the sub-systems.

- **Level.** Show containers by default. Show components when the story happens mostly inside one container.
- **Same names, same relationships.** Participants have exactly the names and types of the structural diagrams, and those inside the system are grouped in a `box` for its boundary.
- **Numbered steps.** Use `autonumber`, so that notes can cite steps (`Step 5: …`) and reviewers can comment on them.
- **Arrows.** `->>` for a synchronous request, `-)` for an asynchronous message, `-->>` for a response, drawn only when it carries something the reader needs. Labels follow the notation: intent, then `[technology]`.
- **Failure paths belong in the diagram.** Each acceptance criterion and each edge case that touches the story is visible, as an `alt` or `opt` block whose condition cites its reference (`alt Report over the limit (EC-1)`).
- **Keep it readable.** About 10 participants and 15 messages at most. Beyond that, draw the main path, and give the long branch its own diagram (`US-1, failure of the export`), or go up a level.

When several stories share a sequence, draw it once as its own diagram, and show it as one step in the others (`Note over api,db: Same as US-1, steps 3 to 6`). Each story still has its own diagram.

By type of spec:

- **Feature:** the workflow each story needs.
- **Bug fix:** the actual (broken) workflow, then the corrected one, with the fault marked. Most fixes only need these and the Container diagram with the sub-systems involved.
- **Refactoring:** the maintainer's stories get their workflows on the target structure, and the workflows that must stay identical get a before and an after.

Edge cases that belong to no story, and constraints with a runtime effect (an authentication flow, a retry policy), get a dynamic diagram of their own, named after their reference (`EC-3: …`), when the design has something to show. When the spec has rollout phases and the sub-systems differ from one phase to the next, draw a Container diagram for each phase.

**Check the diagrams against each other:**

- Each arrow of a dynamic diagram (apart from responses) is a relationship in the structural diagram at its level, in the same direction. Add it there, or correct the workflow.
- Each relationship of the Container diagram that's new or changed is used by at least one workflow. Otherwise, it isn't needed: remove it, or find the story that's missing.
- Each new or changed sub-system takes part in at least one workflow. Otherwise, it isn't needed, or the spec has a gap to raise.

## Design decisions

Most decisions follow the codebase's existing patterns: use them, and don't list them. A choice needs an entry when it's costly to reverse, shapes several sub-systems or their contracts, adds a technology, or has lasting consequences on cost, security or operations. For each one:

1. Give at least two real, workable options, one line each, with the main trade-off against the spec's constraints and any coupling it creates. If there's only one, say why.
2. Recommend one, and let the user decide. If it can't be decided yet, because it rests on something uncertain, name the option the diagrams assume, and leave the question open.
3. Link the decision to the diagrams it shapes.

The design doesn't write ADRs or test feasibility: those belong to the [`plan` skill](../plan/SKILL.md#technical-choices). Give it what it needs: flag each entry that deserves an ADR, and each risk whose feasibility is uncertain.

## AI-XF format

The design uses the same document format as the specs: an [AI-XF 0.5](https://github.com/DavidROliverBA/ai-xf-format/blob/main/SPEC.md) concept at Level 2 (and therefore an OKF 0.2 concept). Follow the [AI-XF format section of the specs skill](../specs/SKILL.md#ai-xf-format) for frontmatter rules, timestamps, actors, footnotes, links, reserved file names, bundle files and validation, with these differences:

- `type: Design`.
- `id` and filename stem: `<spec-id>-design` (e.g. `feature-csv-export-design`); for a spec without an `id`, use its filename stem.
- `title`: `Design: <spec title>`, identical to the H1.
- The spec is referenced with a `references` link to its `id` when it's in the same bundle (mirrored by a body link), or with a `sources` entry otherwise.
- `status`: `draft` until its pull request is merged, then `stable`, with `verified` entries added on merge, exactly as for a spec (see [Publishing in the specs skill](../specs/SKILL.md#publishing)). When the spec changes after the design was approved, the design goes back to `draft` until it's updated.

## Process

### 1. Load the spec

1. Get the spec from the user, read the whole file, and read the local material it links to.
2. Decide whether it's [usable](#usable-spec), going through the `specs` skill if it isn't and the user accepts.
3. List its user stories, or the scenarios mapped from its requirements, along with its constraints, boundaries, edge cases, rollout phases and open questions. Use the spec's own IDs when it has them; otherwise the [conventions of the tests skill](../tests/SKILL.md#2-inventory) (`US-n/AC-m`, `EC-n`, `C-n`).
4. Settle the codebase scope (see [Rules](#rules)), and check that every repository in it is reachable.
5. If the design already exists, read it and update it rather than starting over.

### 2. Explore

If working in a codebase, explore every repository in the codebase scope (a code index such as CodeGraph first, when you have one and the repository is indexed):

- the sub-systems and external systems that exist today, and how they talk;
- the technologies and patterns to follow;
- the components in the spec's Blast radius, and what depends on them;
- any existing architecture documents, ADRs or diagrams, whose names and conventions take precedence over your own.

### 3. Agree on the shape

Before detailing anything, show the user the System context and Container diagrams, in the conversation, together with:

- the grain of the sub-systems, and each one's status;
- the [design decisions](#design-decisions), each with its options and a recommendation;
- any cycle or doubtful coupling.

Update the diagrams until the user confirms the shape. Every workflow rests on it.

### 4. Draw the workflows

1. Draw the [dynamic diagram of each user story](#workflows), then the Component and Deployment diagrams they turn out to need.
2. Cross-check the diagrams, and update the structural ones. A workflow often reveals a sub-system or relationship that's missing.
3. Add the fewest notes that clarify the diagrams (see [Diagrams first](#diagrams-first)).
4. Fill in Coverage. Any spec item the design doesn't reach is either added to a diagram, or raised with the user.

### 5. Review with the user

1. Confirm the file location with the user: propose the spec's folder (the same AI-XF bundle), next to the spec, named `<spec-id>-design.md`, or follow an existing convention in the project.
2. Write the file with `status: draft`, following [template.md](template.md) exactly: same frontmatter, same sections, same order. Check that its diagrams render. Then walk the user through it, diagram by diagram, and ask for corrections.
3. Iterate until the user says the draft is ready for review. The design stays `status: draft`: approving its content is the reviewers' job, in the pull request.
4. Update the bundle's `index.md` and `log.md` if they exist, and run the validator if available (see the specs skill).
5. Publish the draft as a pull request, as described in [Publishing in the specs skill](../specs/SKILL.md#publishing), with a branch named after the design (e.g. `docs/<spec-id>-design`). When the spec's pull request is still open, ask whether to add the design to it or to open a separate one.
6. In your final reply, give the pull request's URL, and list:
   - the design decisions, and those still open;
   - the open questions left in the design, and the diagrams they affect;
   - any remaining cycle or justified coupling;
   - the gaps found in the spec, and whether it was updated;
   - any diagrams that couldn't be rendered;
   - any side issues you noticed.

## Quality checklist

Before presenting the draft, check that:

- [ ] The design covers exactly one spec, given by the user, and stays inside its Boundaries.
- [ ] A spec that isn't from the `specs` skill was assessed as usable, or the user chose to go on; a spec from the `specs` skill wasn't questioned.
- [ ] Hiding the words, the design can still be followed from the diagrams. No prose repeats a diagram, and no diagram has more than three notes.
- [ ] System context and Container diagrams are present; a Component or Deployment diagram is present where needed, or its section says why not.
- [ ] Every diagram has a title stating its type and scope, and a key (its own, or the document's for dynamic diagrams).
- [ ] Every element has a name, a type and a description, and every container and component has a technology.
- [ ] Every arrow is one-way and labelled with its intent, and every arrow between containers states its technology. No bare "Uses".
- [ ] Each queue or topic is its own container, or named on the arrow.
- [ ] Every sub-system involved is in the Container diagram and in the sub-systems table, with its status.
- [ ] Every user story has its own dynamic diagram, with numbered steps.
- [ ] Every acceptance criterion and edge case is shown in a diagram or raised with the user, and Coverage traces it.
- [ ] Dynamic and structural diagrams agree: same names, same relationships, no new or changed sub-system or relationship unused by a workflow.
- [ ] No cycle between sub-systems without a recorded reason, and no coupling without a reason.
- [ ] Every design decision has at least two workable options (or the reason there's only one), a recommendation, and the user's decision or the option assumed meanwhile.
- [ ] Every element is named as in the code, or marked as new; the requirements are the spec's, with nothing invented.
- [ ] No plan, task list, code, field-level schema or endpoint listing.
- [ ] The codebase scope matches the spec's, is confirmed by the user, and is recorded with the branch and commit analysed per repository.
- [ ] Every diagram renders, or the final reply says it couldn't be checked.
- [ ] The design is `status: draft`, with no new `verified` entry: only the merge of its pull request makes it `stable`.
- [ ] AI-XF Level 2: same checks as the specs skill, with `type: Design` and a resolvable link or source to the spec.

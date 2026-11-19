---
name: adr
description: Turn a given technical spec (a single markdown file, such as one written by the specs skill) into the architecture decision records (ADRs) for its strong technical choices - one per decision, each with at least two workable options, their trade-offs, a recommendation and the user's decision (AI-XF 0.5 markdown, a superset of OKF 0.2). Follows the project's ADR convention when it has one. Use when the user asks for ADRs, architecture decisions or the technical choices of a spec, or invokes the adr skill directly (e.g. /sdlc:adr in Claude Code).
---

# ADR

Produce the architecture decision records for one spec: one ADR per strong technical choice the spec leaves open. The spec says what is built; the ADRs record how, for the choices that matter, and why. They are written before the design and the implementation plan, which build on them.

Never decide for the user: propose options and a recommendation, and record only what the user decided.

## Rules

- **One spec.** The input is one technical spec. When it isn't usable (ambiguous, contradictory, or missing what the choices depend on), offer to run it through the [specs skill](../specs/SKILL.md) first, as the [plan skill](../plan/SKILL.md) does.
- **Strong choices only.** Most choices are routine: follow the codebase's existing patterns, and don't write them up. A strong choice is one that's costly to reverse, that shapes several sub-systems or their contracts, that adds a technology or a dependency, or that has lasting consequences on cost, security or operations.
- **An ADR where appropriate:** when the choice will outlive this spec, is hard to reverse, or affects work beyond this spec, or when the team will one day ask why it was made. A strong choice that doesn't meet that bar is reported in your final reply, for the plan, not written as an ADR.
- **One decision per ADR.** Two choices that can be made independently are two ADRs.
- **Real options.** At least two workable technical solutions for each choice: don't add a straw man to make up the number. If you can only find one, say why it has no credible alternative, and ask the user whether they see one.
- **Grounded in the code.** Read the codebase scope recorded in the spec's Blast radius: the options and their trade-offs reflect the code as it is, its existing patterns and dependencies. Follow the spec's rules on working copies (see the [specs skill](../specs/SKILL.md#codebase-scope)).
- **The user decides.** Recommend one option with your reasons; the decision, and who took it, is the user's. When a decision rests on a hard problem not settled yet (something that might not work, or isn't known), don't force it: say so, name the experiment that would settle it, and leave the choice to the [plan skill](../plan/SKILL.md#hard-problems), whose feasibility step writes the ADR.
- **No design or plan.** No diagrams of the whole system, no steps: those come next, with the design and plan skills.
- **Accepted ADRs are not rewritten.** An existing ADR the spec's choices contradict gets a new ADR that supersedes it (`supersedes` link), never an edit.

## Location and format

- Look for an existing ADR convention in the codebase scope (a folder such as `docs/adr/`, `adr/` or `docs/decisions/`, or the project's agent instructions), and follow its location, numbering and format.
- Without one, propose an `adr/` folder in the spec's AI-XF bundle, and confirm it with the user.
- Without an existing format, use [template.md](template.md): one file per decision, `adr-<slug>.md`, an AI-XF concept like the spec. Read the existing ADRs there first: a choice already decided isn't decided again, and `id`s stay unique.

## AI-XF format

Each ADR is an [AI-XF 0.5](https://github.com/DavidROliverBA/ai-xf-format/blob/main/SPEC.md) concept at Level 2, following the [AI-XF format section of the specs skill](../specs/SKILL.md#ai-xf-format), with:

- `type: ADR`, shown as `**ADR**` on the line under the title;
- `id` and filename stem `adr-<slug>`, or the project's numbering;
- a `references` link to the spec's `id` when it is in the same bundle (mirrored by the body link in Context), or a `sources` entry otherwise;
- `status: draft` means proposed, `stable` (set on merge) accepted, and `deprecated` with a `superseded-by` link superseded.

## Process

1. Read the spec, its linked documents (the requirements document it `specifies`, its dependencies), and the existing ADRs.
2. Read the code in the spec's codebase scope, where the choices land.
3. List the strong choices the spec leaves open, and propose them to the user, saying for each whether it warrants an ADR. Agree on the list before going further.
4. For each agreed choice, one at a time: present the options with their trade-offs against the spec's constraints and the coupling each creates, and your recommendation. Use your structured question tool (e.g. `AskUserQuestion` in Claude Code) for the decision. Write the ADR as soon as the user decides, with `status: draft`, following [template.md](template.md) or the project's format exactly.
5. Update the bundle's `index.md` and `log.md` if they exist, and run the validator if available (see the specs skill).
6. Publish the ADRs as a pull request, as described in [Publishing in the specs skill](../specs/SKILL.md#publishing), with a branch named after the spec (e.g. `docs/<spec-id>-adrs`) and a commit such as `docs(adr): add the ADRs of <spec-id>`. The pull request's review and merge accept them.
7. In your final reply, give the pull request's URL, and list the ADRs and their decisions, the strong choices left to a feasibility step, those that didn't warrant an ADR, and the next steps: the design, test plan and implementation plan (e.g. `/sdlc:design`, `/sdlc:tests`, `/sdlc:plan <path to the spec>` in Claude Code), which build on these ADRs.

## Quality checklist

Before publishing, check that:

- [ ] Every ADR is for one strong choice of this spec, agreed with the user as warranting one.
- [ ] Every ADR has at least two workable options, each with its trade-offs and the coupling it creates, or says why there is no credible alternative.
- [ ] Every decision was taken by the user, and the ADR says who chose and why the option beats the others.
- [ ] Consequences say what becomes easier and harder, what is ruled out, and what would make the decision worth revisiting.
- [ ] No choice already decided by an accepted ADR is decided again; a contradicted one is superseded, not edited.
- [ ] The project's ADR convention is followed when it has one.
- [ ] Every ADR is `status: draft`, with no new `verified` entry.
- [ ] AI-XF Level 2: same checks as the specs skill, with `type: ADR` and a resolvable link or source to the spec.

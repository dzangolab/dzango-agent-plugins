# Implement templates

Remove the HTML comments in the final text, and any line that doesn't apply. When the repository has a pull request template, fill in its sections and add these where they fit.

## Pull request description

````markdown
<!-- One or two sentences: the step's deliverable, as the plan states it. -->

Step <n> of [Implementation plan: <spec title>](<link to the plan>).

**Delivers spec items:** <US-1/AC-1, EC-2>

## Stack

- **Base:** <#n (step m), or the default branch>
- **Merge after:** <#n, #m, or "Nothing">
- **Stacked on this:** <#n, #m, or "Nothing yet">

<!-- Only when the step has several unmerged dependencies -->

This pull request also contains #<n> (step <m>), so its diff shows that work until it's merged.

## Tests

<!--
One line per expected test, from the plan's "Tests to pass". Check a box only for a test seen to pass, in this run.
Not run: leave it unchecked with the reason. Already failing before the step: leave it unchecked and say so.
-->

- [x] <test ID>: `<test name or path>`
- [x] <test ID from step m>: `<test name or path>`
- [x] Regression: `<suite>`
- [x] Verification: <check> (<spec item>)
- [x] Repository checks: <build, types, linter>
- [ ] <test ID>: not run: <reason>

**Run with:** `<command>`, on `<short commit>`: <n passed, n failed>

**CI:** <green | not triggered: the base isn't the default branch | failing: why>

## Decisions made

<!-- Small decisions the plan doesn't cover, so that reviewers can challenge them. "None" if there are none. -->

- <decision>: <why>

## Stopped

<!-- Only for a stopped step, whose pull request stays a draft. -->

<What stopped the step, what was tried, and what the plan would need to say.>

🤖 <the attribution your agent or the repository requires>
````

## Feasibility pull request description

````markdown
**Do not merge: throwaway code.** Feasibility step <n> of [Implementation plan: <spec title>](<link to the plan>).

- **Question:** <the question, as the plan states it>
- **Test:** <how it was run, on what data or at what load>
- **Run with:** `<command>`
- **Result:** <the figures or the outcome>
- **Answer:** <Pass | Fail | No answer>: <what the plan's pass or fail criterion says about it>
- **Then:** <what follows: the steps that can start, the ADR written, or the question put to the user>
````

## Run report

The final reply, with the most important facts first.

````markdown
<One or two sentences: how many steps have a pull request ready for review, and what's holding back the rest.>

| Step | Pull request | Base | Status | Expected tests |
| ---- | ------------ | ---- | ------ | -------------- |
| 1 | #n | main | Ready | 4 of 4 |
| 2 | #n | #n | Ready; CI not triggered | 6 of 6 |
| 3 | #n, draft | #n | Needs checking | 2 of 3: AT-1 needs staging |
| 4 | #n, draft | main | Stopped | 1 of 2 |
| 5 | None | | Not started: depends on 4 | |
| 6 | None | | Needs a person | |

<!-- Status: Ready | Needs checking | Stopped | Blocked | Not started | Needs a person | Feasibility: pass, fail or no answer -->

**Merge order:** <the order that works, e.g. "#1, then #2 and #3 in either order, then #5">

**All together:** <the whole test plan on the merged last steps: n passed, n failed, or why it couldn't run>

**What needs you:**

- <each stopped step: what stopped it and what the plan needs>
- <each step for a person: who does what>
- <pull requests with changes requested>

**Decisions made without the plan:** <the ones worth a reviewer's attention, with their pull request>

**Already failing:** <tests that failed before any change, with their step>

**Prerequisites not checked:** <each one>

**Side issues:** <what you noticed and left alone>
````

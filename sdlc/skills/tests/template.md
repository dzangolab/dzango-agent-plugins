# Test plan template

Use these sections, in this order. Section guidance is in the HTML comments; remove the comments in the final document. Keep every section so the numbering stays stable: for a test level (4 to 8) that isn't used, write "Not used: see Strategy." as its only content.

````markdown
---
description: Test plan for <spec title>
generated:
  at: <ISO 8601 datetime with UTC offset>
  by: claude-code/<version>
id: <spec-id>-test-plan
# When the spec is in the same bundle; mirror it with the body link in section 1
links:
  - note: The spec this plan tests
    rel: references
    to: <spec id>
# When the spec is outside the bundle (instead of the link above), and for other sources
sources:
  - id: <short-id>
    resource: <URL or path>
    title: <source title>
status: draft
tags:
  - <tag>
title: "Test plan: <spec title>"
type: Test plan
# Add on approval:
# verified:
#   - at: <ISO 8601 datetime with UTC offset>
#     by: human:<id>
---

# Test plan: <spec title>

## 1. Scope

<!--
The spec under test, its type, and in one or two sentences what this plan proves.
-->

- **Spec:** [<spec title>](/<spec-file>.md)
- **Spec type:** <Feature | Bug fix | Refactoring>
- **Proves:** <one or two sentences>

## 2. Strategy

<!--
The levels used, the tool and location of each (from the codebase's existing setup, or as agreed with the user),
and what each level runs for real vs replaces with test doubles. List the levels left out, and why.
-->

| Level | Tool | Location | Runs for real | Test doubles |
| ----- | ---- | -------- | ------------- | ------------ |
| Acceptance | <tool> | <path> | <components> | <doubles, or "None"> |

**Levels not used:** <level: reason, or "None">

## 3. Coverage

<!--
Every testable spec item, and the tests that prove it. Status: Covered | Blocked (open question) | Not tested (see section 11).
State the reference convention, e.g. "Spec items are referenced by their IDs in the spec; edge cases and constraints
are numbered in document order (EC-n, C-n)."
-->

<reference convention>

| Spec item | Summary | Tests | Status |
| --------- | ------- | ----- | ------ |
| US-1/AC-1 | <short summary> | AT-1, UT-2 | Covered |

## 4. Acceptance tests

<!--
Declarative Gherkin in the domain's words: what the user does and sees, no clicks, selectors, URLs or internal names.
One Feature per spec; one Scenario per acceptance criterion or edge case, or a Scenario Outline with Examples for
equivalence classes. Tag every scenario with its test ID and the spec items it covers. Reuse the same step wording.
Bug fix: tag the reproducing scenario @reproduces-bug if it is at this level.
-->

```gherkin
Feature: <spec title>
  As a <persona>, I want <capability>, so that <benefit>.

  Background:
    Given <shared context>

  @AT-1 @US-1/AC-1
  Scenario: <behaviour in a few words>
    Given <context>
    When <action>
    Then <observable outcome>

  @AT-2 @EC-1
  Scenario Outline: <behaviour across equivalence classes>
    Given <context with <param>>
    When <action>
    Then <outcome with <result>>

    Examples:
      | param | result |
      | <value> | <expected> |
```

## 5. Non-functional tests

<!--
One row per constraint that needs its own check: performance, security, accessibility, compatibility, reliability,
observability. The threshold comes from the spec or the user, with its unit and measurement conditions.
-->

| ID | Subject | Given | When | Then (threshold) | Test doubles | Covers |
| -- | ------- | ----- | ---- | ---------------- | ------------ | ------ |
| NF-1 | <endpoint, page, job> | <load, data, environment> | <action> | <measurable result> | <doubles, or "None"> | C-1 |

## 6. Integration tests

<!--
Two or more real components together: API contracts, database, queues, external services, configuration.
Name the subject as in the code (endpoint, module, table, queue).
-->

| ID | Subject | Given | When | Then | Test doubles | Covers |
| -- | ------- | ----- | ---- | ---- | ------------ | ------ |
| IT-1 | <components> | <state> | <action> | <expected result> | <doubles, or "None"> | US-1/AC-2 |

## 7. Component tests

<!--
One component (module, service, UI component) in isolation, its collaborators replaced by test doubles.
-->

| ID | Subject | Given | When | Then | Test doubles | Covers |
| -- | ------- | ----- | ---- | ---- | ------------ | ------ |
| CT-1 | <component path or name> | <state> | <action> | <expected result> | <doubles> | EC-2 |

## 8. Unit tests

<!--
One function, class or rule. Use one row with several values for equivalence classes and boundary values.
Bug fix: mark the reproducing test "must fail before the fix and pass after it" in the Then column, if it is at this level.
Refactoring: mark characterisation tests "write and run green before the refactoring".
-->

| ID | Subject | Given | When | Then | Test doubles | Covers |
| -- | ------- | ----- | ---- | ---- | ------------ | ------ |
| UT-1 | <path#function> | <inputs: values or classes> | <call> | <expected result per input> | <doubles, or "None"> | EC-3 |

## 9. Regression

<!--
Existing suites or tests to re-run because the blast radius touches them, and why. "None" for a new system.
-->

- <suite or path>: <blast-radius component it guards (BR-n)>

## 10. Test data and environments

<!--
Fixtures, seed data, accounts and roles, feature flags, environments and external services the tests need,
named so they can be created once and shared. Nothing invented: each item comes from the spec, the code or the user.
-->

| Item | Used by | Description |
| ---- | ------- | ----------- |
| <fixture, account, flag, environment> | AT-1, IT-1 | <what it contains or how it is set up> |

## 11. Not tested

<!--
Spec items or risks deliberately left without a test, and why, agreed with the user. "None" if everything is covered.
-->

| Spec item | Reason |
| --------- | ------ |
| <spec item> | <why no test, e.g. verified by review, covered by an existing suite, out of the spec's boundaries> |

<!-- Footnotes: one per cited source, label = sources[].id -->

[^short-id]: <source title>, <URL or path>
````

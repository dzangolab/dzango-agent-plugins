# Design template

Use these sections, in this order. Section guidance is in the HTML comments; remove the comments in the final document. Keep every section so the numbering stays stable. The diagrams are written to render as they are: replace their placeholder names, and remove the elements, classes and key entries that aren't used.

````markdown
---
description: Design for <spec title>
generated:
  at: <ISO 8601 datetime with UTC offset>
  by: <agent>/<version>
id: <spec-id>-design
# When the spec is in the same bundle; mirror it with a body link
links:
  - note: The spec this design delivers
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
title: "Design: <spec title>"
type: Design
# Added on merge by okf-approve, never by hand:
# verified:
#   - at: <ISO 8601 datetime with UTC offset>
#     by: human:<GitHub login>
---

# Design: <spec title>

## 1. Scope

<!--
Bullets only. Codebase scope: one bullet per repository (with the app or package path in a monorepo), "Not analysed:
<reason>" instead of the branch and commit for a repository left out or not reachable.
Sub-system grain: Container, or the other level used, and why.
Assumptions: only for a spec not written by the specs skill, when the user chose to go on without it; otherwise remove.
-->

- **Spec:** [<spec title>](/<spec-file>.md)
- **Spec type:** <Feature | Bug fix | Refactoring>
- **Sub-system grain:** <Container | Software system | Component>
- **Codebase scope:**
  - <repository>[/<app or package path>] @ <branch> (<short commit>)

## 2. Key

<!--
Applies to the dynamic diagrams, and explains any abbreviation used in the document. The structural diagrams carry their
own key too, so they can be read when they are copied elsewhere.
-->

| Notation | Meaning |
| -------- | ------- |
| Solid arrow, closed head | Synchronous request |
| Solid arrow, open head | Asynchronous message |
| Dashed arrow | Response |
| Numbered circle | Step number, cited by the notes |
| Outer box | Boundary of the software system |
| Colour of a participant | No meaning: set by the renderer |

## 3. System context

<!--
C4 level 1. People and software systems only: no containers. Up to three notes below the diagram, only for what it
can't show. Status in the type line: New, Changed or Unchanged.
-->

```mermaid
---
title: "System context diagram: Name of the system"
config:
  flowchart:
    wrappingWidth: 320
---
flowchart TB
  person["<b>Persona</b><br/>[Person]<br/>What they want from the system"]:::person
  system["<b>Name of the system</b><br/>[Software system] Changed<br/>What it does for them"]:::changed
  external["<b>External system</b><br/>[Software system] Unchanged<br/>What it does"]:::external
  person -->|"Intent of the relationship"| system
  system -->|"Intent of the relationship"| external
  subgraph key["Key"]
    direction LR
    kp["Person"]:::person ~~~ kc["Our system"]:::changed ~~~ ke["System outside the scope"]:::external
  end
  external ~~~ key
  classDef person fill:#08427b,stroke:#052e56,color:#fff
  classDef changed fill:#1168bd,stroke:#f59e0b,stroke-width:4px,color:#fff
  classDef external fill:#999,stroke:#6b6b6b,color:#fff
  style key fill:none,stroke:#999
```

## 4. Sub-systems

<!--
C4 level 2: every sub-system involved, including the unchanged ones a workflow goes through. Data stores as cylinders,
each queue or topic as its own container. Arrows: intent, then [technology]; no technology needed for an arrow from a
person. For a refactoring: one diagram for today, one for the target. For rollout phases that change the sub-systems:
one diagram per phase. Notes only for what the diagram can't show, such as the reason for a coupling.
Table: one row per sub-system, named as in the diagram and the code. It indexes the diagram, so it doesn't repeat each
element's responsibility or technology. Status: New | Changed | Unchanged | External.
Location: repository and path, or "New: <where it will live>". Stories: the workflows it takes part in, in section 6.
-->

```mermaid
---
title: "Container diagram: Name of the system"
config:
  flowchart:
    wrappingWidth: 320
---
flowchart TB
  person["<b>Persona</b><br/>[Person]<br/>What they want from the system"]:::person
  subgraph system["Name of the system [Software system]"]
    web["<b>Web app</b><br/>[Container: Technology] New<br/>Its responsibility"]:::new
    api["<b>API</b><br/>[Container: Technology] Changed<br/>Its responsibility"]:::changed
    db[("<b>Database</b><br/>[Container: Technology] Changed<br/>What it stores")]:::changed
    queue[["<b>Queue name</b><br/>[Container: Technology] New<br/>What passes through it"]]:::new
    worker["<b>Worker</b><br/>[Container: Technology] New<br/>Its responsibility"]:::new
    other["<b>Other service</b><br/>[Container: Technology] Unchanged<br/>Its responsibility"]:::unchanged
  end
  external["<b>External system</b><br/>[Software system] Unchanged<br/>What it does"]:::external
  person -->|"Intent of the relationship"| web
  web -->|"Intent<br/>[Technology]"| api
  api -->|"Intent<br/>[Technology]"| db
  api -->|"Intent<br/>[Technology]"| other
  api -->|"Intent<br/>[Technology]"| queue
  queue -->|"Intent<br/>[Technology]"| worker
  worker -->|"Intent<br/>[Technology]"| external
  subgraph key["Key"]
    direction LR
    kp["Person"]:::person ~~~ ku["Unchanged"]:::unchanged ~~~ kh["Changed"]:::changed ~~~ kn["New"]:::new ~~~ ke["System outside the scope"]:::external
  end
  external ~~~ key
  classDef person fill:#08427b,stroke:#052e56,color:#fff
  classDef unchanged fill:#438dd5,stroke:#2e6295,color:#fff
  classDef changed fill:#438dd5,stroke:#f59e0b,stroke-width:4px,color:#fff
  classDef new fill:#438dd5,stroke:#22c55e,stroke-width:4px,color:#fff
  classDef external fill:#999,stroke:#6b6b6b,color:#fff
  style system fill:none,stroke:#666,stroke-dasharray:6 4
  style key fill:none,stroke:#999
```

| Sub-system | Status | Location | Stories |
| ---------- | ------ | -------- | ------- |
| <name> | <status> | <repository>/<path> | <US-1, US-2> |

## 5. Components

<!--
C4 level 3, one diagram per container whose inside decides how a story works, in the same order as the table above.
Arrows to the other containers and systems it deals with are drawn as supporting elements.
If none is needed, write "Not needed: <reason>." instead.
-->

```mermaid
---
title: "Component diagram: API"
config:
  flowchart:
    wrappingWidth: 320
---
flowchart TB
  web["<b>Web app</b><br/>[Container: Technology] New<br/>Its responsibility"]:::new
  subgraph api["API [Container]"]
    handler["<b>Handler</b><br/>[Component: Technology] New<br/>Its responsibility"]:::componentNew
    service["<b>Service</b><br/>[Component: Technology] Changed<br/>Its responsibility"]:::componentChanged
    store["<b>Repository</b><br/>[Component: Technology] Unchanged<br/>Its responsibility"]:::component
  end
  db[("<b>Database</b><br/>[Container: Technology] Changed<br/>What it stores")]:::changed
  web -->|"Intent<br/>[Technology]"| handler
  handler -->|"Intent"| service
  service -->|"Intent"| store
  store -->|"Intent<br/>[Technology]"| db
  subgraph key["Key"]
    direction LR
    kc["Component, unchanged"]:::component ~~~ kh["Component, changed"]:::componentChanged ~~~ kn["Component, new"]:::componentNew ~~~ ko["Other container"]:::changed
  end
  db ~~~ key
  classDef changed fill:#438dd5,stroke:#f59e0b,stroke-width:4px,color:#fff
  classDef component fill:#85bbf0,stroke:#5d82a8,color:#000
  classDef componentChanged fill:#85bbf0,stroke:#f59e0b,stroke-width:4px,color:#000
  classDef componentNew fill:#85bbf0,stroke:#22c55e,stroke-width:4px,color:#000
  classDef new fill:#438dd5,stroke:#22c55e,stroke-width:4px,color:#fff
  style api fill:none,stroke:#666,stroke-dasharray:6 4
  style key fill:none,stroke:#999
```

## 6. Workflows

<!--
One sub-section per user story, in the spec's order, each with its own dynamic diagram. Each acceptance criterion and
edge case of the story is visible: alt or opt blocks cite their reference. Participant names and types as in sections
4 and 5. Then edge cases and constraints that belong to no story, when they have a workflow worth showing.
Up to three notes below a diagram, citing step numbers, only for what the diagram can't show.
Bug fix: the actual workflow, then the corrected one. Refactoring: before and after, for workflows that must stay identical.
-->

### US-1: <short name>

```mermaid
---
title: "Dynamic diagram: US-1 short name"
---
sequenceDiagram
  autonumber
  actor person as Persona<br/>[Person]
  box Name of the system [Software system]
    participant web as Web app<br/>[Container: Technology]
    participant api as API<br/>[Container: Technology]
    participant db as Database<br/>[Container: Technology]
    participant queue as Queue name<br/>[Container: Technology]
    participant worker as Worker<br/>[Container: Technology]
  end
  participant external as External system<br/>[Software system]
  person->>web: Intent
  web->>api: Intent [Technology]
  alt Condition of the edge case (EC-1)
    api-->>web: Response [Technology]
    web-->>person: Response
  else Condition of the main path (US-1/AC-1)
    api->>db: Intent [Technology]
    api-)queue: Intent [Technology]
    api-->>web: Response [Technology]
    queue-)worker: Intent [Technology]
    worker->>external: Intent [Technology]
  end
```

- Step 4: <only what the diagram can't show, e.g. why this step comes before the next>

> **Open question:** <only when the user deferred one: what's undecided, and what the diagram assumes>

## 7. Deployment

<!--
C4 deployment diagram, one per environment that differs in a way that matters. Deployment nodes as nested subgraphs,
containers inside them. If none is needed, write "Not needed: <reason>." instead.
-->

```mermaid
---
title: "Deployment diagram: Name of the system, production"
config:
  flowchart:
    wrappingWidth: 320
---
flowchart TB
  subgraph cloud["Cloud account [Deployment node: Provider]"]
    subgraph compute["Compute [Deployment node: Technology]"]
      api["<b>API</b><br/>[Container: Technology]<br/>Its responsibility"]:::container
    end
    subgraph data["Storage [Deployment node: Technology]"]
      db[("<b>Database</b><br/>[Container: Technology]<br/>What it stores")]:::container
    end
  end
  api -->|"Intent<br/>[Technology]"| db
  subgraph key["Key"]
    direction LR
    kc["Container"]:::container ~~~ kd["Outer boxes: deployment nodes"]:::note
  end
  db ~~~ key
  classDef container fill:#438dd5,stroke:#2e6295,color:#fff
  classDef note fill:none,stroke:none
  style cloud fill:none,stroke:#666
  style compute fill:none,stroke:#666
  style data fill:none,stroke:#666
  style key fill:none,stroke:#999
```

## 8. Design decisions

<!--
Strong choices only: costly to reverse, shaping several sub-systems or their contracts, adding a technology, or with
lasting consequences. At least two workable options per choice, one line each: the option, then its main trade-off.
If there are none, write "None: the design follows the codebase's existing patterns."
Decision: the user's decision and its reason, or "Open; the diagrams assume <option>".
For the plan: "ADR" when the choice deserves one, "Feasibility: <what's uncertain>" when it needs testing, or "None".
-->

| Choice | Options and trade-offs | Decision | Shapes | For the plan |
| ------ | ---------------------- | -------- | ------ | ------------ |
| <choice> | <option A: trade-offs, coupling><br/><option B: trade-offs, coupling> | <decision> | <sections or diagrams> | <ADR, Feasibility, None> |

## 9. Coverage

<!--
Every user story, then every other spec item, and the diagram that shows it. Use the spec's IDs, or the conventions of
the tests skill: US-n/AC-m, EC-n, C-n. Where: the workflow and steps, or the diagram and element. A constraint with no
architectural effect is "No effect on the design". An item the design can't reach yet is raised with the user first,
and then marked "Open question".
-->

| Spec item | Summary | Where | Sub-systems |
| --------- | ------- | ----- | ----------- |
| US-1/AC-1 | <short summary> | US-1, steps 1 to 8 | <names> |
| EC-1 | <short summary> | US-1, steps 3 and 4 | <names> |
| C-1 | <short summary> | Section 7, Database | <names> |

<!-- Footnotes: one per cited source, label = sources[].id -->

[^short-id]: <source title>, <URL or path>
````

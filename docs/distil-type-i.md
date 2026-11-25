# `distil-type-i` — what a Type I machine may do

`type: resource.reference` · `spec: agent.reference` · subordinate to `docs/follow-agent-contract.md`, which governs on any conflict.

A Type I AI is a reactive machine. In Arend Hintze's four types (2016) it is the most basic: it has no memories and does not use past experience to inform the present decision; it looks only at the situation as it stands now. tulip's autopilot (`systems/fly-autopilot.md`) is built to be exactly this kind of machine, and no more. This file distils the operating principles of a Type I system into rules an agent can check, each with the code that enforces it.

## Specification

```json { type: notebook.cell }
{
  "id": "docs/distil-type-i.md",
  "type": "resource.reference",
  "role": "state the operating principles of a reactive (Type I) machine as checkable rules",
  "reads": ["a heading", "one hinge"],
  "writes": [],
  "must": ["present percept only", "fixed table", "same percept, same act", "no model of the Reader or the story", "narrow envelope", "authority delegated by the Reader", "zero bits per reflex"],
  "check": ["T7", "T13"]
}
```

## Graph

- ← [follow-agent-contract.md](follow-agent-contract.md)
- → [../systems/fly-autopilot.md](../systems/fly-autopilot.md)

## Indications

- An agent must act at a hinge without asking the Reader, and the act must stay explainable by a rule the Reader set.
- A reviewer asks whether some automation in a frame is a reflex (allowed under a heading) or a choice (only the Reader's).

## Contraindications

- Not a licence to choose. A reflex is the Reader's table applied to one hinge; an agent that picks by taste, sampling or prediction is refused (`self_choice`, `pure_chance`).
- Not Type II or above. Anything that remembers the path, counts visits, learns or models the Reader is refused (`stateful`).
- Not a planner. Deep Blue, Hintze's example, searches forward from the board in front of it; tulip's autopilot does not even do that. It looks up one answer in a table.

## The four types

| Type | Name | Has | tulip role |
|---|---|---|---|
| I | reactive machine | the present percept and a fixed mapping | **Autopilot** (this file) |
| II | limited memory | a window of the recent past | none; refused as `stateful` |
| III | theory of mind | models of others' beliefs and wants | none; the Narrator may only guess with "perhaps" (`invention`) |
| IV | self-awareness | a model of itself | none |

The Engine (`skills/*.md`) is also reactive: a pure function of constraint and key. The Narrator is not a Type I machine; it reads the whole trace, which is why it is allowed only to narrate and never to choose.

## Principles

Each principle is a rule over `reflex` events (contract §1). `percept` is what the machine may look at; `table` is the Reader's heading.

| ID | Principle | Rule | Refusal |
|---|---|---|---|
| P1 | **Present only.** It acts on the current situation and nothing before it. | `percept` is one hinge: its node id and its offered option ids. Never the path, the trace, a text, a counter or the clock. | `stateful` |
| P2 | **Fixed mapping.** Its condition → action table is set before it acts and does not change while it acts. | The table is the body of one Reader `choice` (the heading). No event but a new Reader choice replaces it. | `stateful`, `unengaged` |
| P3 | **Same percept, same act.** It is a function, not a habit. | `reflex.picked = table(percept)` for every reflex under one heading; a percept met twice gets the same answer twice. | `stateful`, T8 |
| P4 | **No model of the world or of others.** It does not know where it is going, what the story means, or what the Reader wants. | A reflex cites only its hinge and its heading. Nothing may describe it as wanting, noticing, preferring or learning. | `invention` |
| P5 | **Narrow envelope.** It runs only in the domain it was built for. | It flies `traverse-tale` hinges only (`frame.json` `autopilot.envelope`). At any other hinge, a terminal, or a hinge that needs a Reader value, it hands back. | `unengaged` |
| P6 | **Delegated authority.** It acts because a person told it to, and the person can take it back at once. | A reflex needs a heading in force. A Reader choice waiting at a hinge always wins over the reflex. | `unengaged`, `self_choice` |
| P7 | **Honest accounting.** What it does is the rule's, not the Reader's. | `reflex.bits = 0`. The Reader's bits for a flight are those of the heading, counted once. The Narrator names every reflex as the autopilot's. | T5, `invention` |

### Consequences the Narrator must state

- **It cannot see a loop.** With no memory (P1), a reactive machine at a self-loop or a two-cycle keeps going round. The frame's counter (`frame.max_incipits`), which belongs to the frame and not to the autopilot, is what stops it. Hintze notes that Deep Blue's one exception to having no past is a chess rule against repetition; tulip keeps the same exception outside the machine.
- **A table is worth less than it looks.** A heading over Queneau's graph answers 19 questions, 19 bits on paper. Because the same node always gets the same answer (P3), only 83 different flights of up to ten incipits exist, so the heading is worth `log2 83 ≈ 6.375` bits. A Reader steering by hand could take 148 different walks of that length (`log2 148 ≈ 7.209`); the difference is what memory would have bought.

## Why "autopilot"

An autopilot holds a heading the pilot set and gives the controls back the moment the pilot takes them. It does not decide where to fly. Dual-process accounts of human thinking call the fast, automatic mode "Type 1" and often describe it as running on autopilot; the reading meets Hintze's at the same point: a response to what is present, with no deliberation.

## Citations

| Pin | Supports |
|---|---|
| [Hintze, *Understanding the four types of AI* (The Conversation, 2016)](https://theconversation.com/understanding-the-four-types-of-ai-from-reactive-robots-to-self-aware-beings-67616) | Type I as reactive machine: no memory, present situation only; Deep Blue; the repetition rule |
| [Brooks, *Intelligence without representation* (1991)](https://people.csail.mit.edu/brooks/papers/representation.pdf) | behaviour from situated condition → action rules without a world model |
| [Subsumption architecture](https://en.wikipedia.org/wiki/Subsumption_architecture) | layered reactive control |
| [Braitenberg vehicle](https://en.wikipedia.org/wiki/Braitenberg_vehicle) | fixed sensor → motor mappings that only look purposeful (P4) |
| [Dual process theory](https://en.wikipedia.org/wiki/Dual_process_theory) | Type 1 processing as fast and automatic |
| [Autopilot](https://en.wikipedia.org/wiki/Autopilot) | holding a set heading; handing control back to the pilot (P6) |
| [Variety (Ashby)](https://en.wikipedia.org/wiki/Variety_(cybernetics)) | the 83-flight bound on a 19-entry table |

## Falsification

| Challenge | Failure signal |
|---|---|
| Autopilot takes the second way at node 11 only on the second visit | `stateful` (P1, P3) |
| Autopilot alternates first and second at nodes 7 and 8 | `stateful` (P1) |
| Autopilot picks at node 7 although the Reader has already picked there | `unengaged` (P6) |
| Autopilot answers the birthday hinge of `combine-lines` | `unengaged` (P5) |
| Chapter says "the autopilot grew bored of node 11" | `invention` (P4) |
| A reflex reports 1 bit | T5 (P7) |
| Heading reported as 19 bits | T5 |

## Licence

MIT, see `LICENSE`.

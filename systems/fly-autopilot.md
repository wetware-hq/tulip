# `fly-autopilot` — the Type I narrative autopilot

`type: resource.system` · `spec: agent.system` · subordinate to `docs/follow-agent-contract.md`, which governs on any conflict.

The autopilot walks a branching tale for You on a heading You set. You give it, once, a table: at each node of the tale, first way or second way. From then on it answers each hinge from that table without asking, while the frame goes on alternating chapters around it, until You take the controls back, the tale ends, the tale hands it a hinge it was not built for, or the frame's counter runs out. It is a Type I machine (`docs/distil-type-i.md`): it sees only the hinge in front of it, it remembers nothing, and every answer it gives is one You wrote in advance.

## Specification

```json { type: notebook.cell }
{
  "id": "systems/fly-autopilot.md",
  "type": "resource.system",
  "role": "answer traverse-tale hinges from a Reader heading, one reflex per hinge, inside compose-frame",
  "reads": ["frame.json autopilot", "skills/traverse-tale.md", "the heading choice", "the current hinge"],
  "writes": ["reflex events into the compose-frame trace"],
  "must": ["engage only on a Reader choice", "percept is the hinge only", "picked = table(percept)", "a waiting Reader choice wins", "hand back outside the envelope", "0 bits per reflex"],
  "check": ["T1-T13"]
}
```

## Graph

- ← [../docs/distil-type-i.md](../docs/distil-type-i.md)
- ← [../skills/traverse-tale.md](../skills/traverse-tale.md)
- ← [../docs/follow-agent-contract.md](../docs/follow-agent-contract.md)
- → [compose-frame.md](compose-frame.md)

## Composition

```toml { type: notebook.cell }
pilot = "the Reader: the heading choice, and take when they want the controls"
autopilot = "the Engine under docs/distil-type-i.md: reflex events, one per tale hinge"
frame = "compose-frame: chapters still alternate; the Narrator names each reflex"
envelope = "frame.json autopilot.envelope = traverse-tale hinges only"
governor = "frame.json frame.max_incipits; it belongs to the frame, not the autopilot"
```

## Indications

- A Reader wants to see what a tale does on a rule they fixed in advance, and how much less that rule is worth than the choices it replaces.
- A workshop on Type I machines: a reactive agent with no memory, shown circling a self-loop until something outside it stops it.

## Contraindications

- Never engage without a Reader `autopilot` pick and a heading (`unengaged`).
- Never answer a hinge outside `traverse-tale`, a terminal hinge, or a hinge that asks for a Reader value such as a birthday or an offset (`unengaged`). Hand back.
- Never let a reflex depend on the path, the visit count, the texts, the clock or the Reader's past picks (`stateful`).
- Never answer a hinge at which the Reader has already picked (`unengaged`).
- Never narrate a reflex as the Reader's choice, or as the autopilot's wish (`invention`).

## Algorithm

### Variables

```toml { type: notebook.cell }
objective = "a flight whose every pick is table(percept) under a heading the Reader chose"

[independent]
heading = "19 digits over {0,1}; digit n answers node n of Queneau's graph; 0 = first way, 1 = second way"
take = "the Reader's choice to end the flight, at any hinge"

[dependent]
reflexes = "one reflex event per tale hinge flown"
flight = "nodes visited from engagement to hand-back"
heading_bits = "log2(distinct flights the offered headings give with the incipits left)"

[controlled]
graph = "skills/traverse-tale.md, corpus:conte structure, fill corpus:kjv Genesis 1:n"
percept = "the hinge: its node id and its offered option ids, nothing else"
presets = "first = 0000000000000000000, second = 1111111111111111111"
```

### The program

It runs inside the loop of `systems/compose-frame.md`, at the step where that loop would `ASK reader`.

```text { type: notebook.cell }
PROGRAM fly-autopilot(reader, frame) {
  ENGAGE {                                              # at a hinge that enters the tale
    offer "tale" and "autopilot" beside the hinge's other options;
    IF choice.picked = "autopilot" THEN {
      heading := ASK reader (Domain choice, frame.json autopilot.heading, cites choice);
      IF heading is not 19 digits over {0,1} THEN REFUSE bad_key; offer the hinge again;
      heading.bits := log2(frame.json autopilot.flights[incipits left]);
      in_force := heading;
    }
  };

  AT each hinge h WHILE in_force {                      # replaces ASK reader
    IF the Reader has picked at h THEN { in_force := none; take that choice as usual };
    ELSE IF h is not a traverse-tale hinge OR h is terminal OR h needs a Reader value
      THEN { in_force := none; ASK reader };            # hand back
    ELSE {
      percept := { node: h.node, offered: ids of h.offered };   # P1: nothing else
      picked  := percept.offered[ in_force[percept.node − 1] ]; # P2, P3
      EMIT reflex { percept, heading: seq of the heading, picked, bits: 0 } CITES [heading, h];
    }
  };

  CONTROL {                                             # every chapter written while in force
    offer { hold, take };                               # silence is hold and is not recorded
    IF take THEN { EMIT choice { offered: [hold, take], picked: "take", bits: 1 } CITES [heading, h];
                   in_force := none; ASK reader at h };
  };
}
```

After a `reflex`, `compose-frame` continues exactly as after a `choice`: `derive` the key (the path so far, `seed: "reader"`, citing the reflex), run the Engine, emit the incipit and its hinge, write the frame chapter. A take ends the flight for the rest of the tale; flying again needs a new `autopilot` pick and a new heading.

### Heading bits

A heading has `2^19 = 524,288` values, but values that differ only at nodes the flight never reaches give the same flight, and a node met twice is answered the same way twice. Counting flights from node 1 until a terminal or the counter:

| Incipits left at engagement | Distinct flights | Heading bits | By hand (distinct walks) |
|---|---|---|---|
| 10 | 83 | 6.375 | 148 (7.209 bits) |
| 9 | 63 | 5.977 | — |
| 8 | 46 | 5.524 | — |

The counts are in `frame.json` `autopilot.flights`. Presets: `first` flies 1 → 4 → 9 → 5 → 6 → 11 and then circles at 11; `second` flies 1 → 2 → 3 → 21 and ends.

### Per-chapter rules (in addition to `compose-frame`)

| Chapter | Must contain | Must not contain |
|---|---|---|
| 2k+1 after a reflex | that the autopilot took the way [reflex e], that it cost You 0 bits, which digit of the heading decided it [heading e], the incipits left, the `hold`/`take` offer | "You chose"; any wish, boredom, intent or noticing by the autopilot |
| 2k+1 at a self-loop or two-cycle | that the autopilot cannot know it is circling, and that only the counter will stop it | a prediction stated as fact beyond what the table and graph fix |
| 2k+1 after a take | that You took the controls [take e], worth 1 bit, and that the rest of the tale is Yours to steer | a reflex |
| last | the flight and the hand-back, reflex count, the heading's bits next to the 19 it looked like | — |

## Worked trace

A Reader opens a frame, engages the autopilot on the `first` preset, watches it circle, and takes the controls. Roots are omitted.

```json { type: notebook.cell }
{"seq":1,"world":"machine","kind":"hinge","body":{"offered":[{"id":"tale","label":"Step into a tale that forks"},{"id":"autopilot","label":"Let the book walk the tale on a heading you set"},{"id":"stop","label":"Close the book"}]},"cites":[]}
{"seq":2,"world":"frame","kind":"chapter","body":"(chapter 1)","cites":[1]}
{"seq":3,"world":"human","kind":"choice","body":{"picked":"autopilot","bits":1.585},"cites":[1]}
{"seq":4,"world":"human","kind":"choice","body":{"offered":{"domain":"19 digits over {0,1}, digit n answers node n","size":524288},"picked":"0000000000000000000","bits":6.375},"cites":[3]}
{"seq":5,"world":"machine","kind":"key","body":{"skill":"traverse-tale","seed":"reader","path":["1"]},"cites":[4]}
{"seq":6,"world":"machine","kind":"text","body":{"constraint":{"skill":"traverse-tale","corpus":"corpus:conte","fill":"corpus:kjv"},"title":"If you take your hands from the page,","text":"(Genesis 1:1)"},"cites":[5]}
{"seq":7,"world":"machine","kind":"hinge","body":{"title":"If you take your hands from the page,","node":"1","offered":[{"id":"first","label":"Take the first way"},{"id":"second","label":"Take the second way"}]},"cites":[6]}
{"seq":8,"world":"frame","kind":"chapter","body":"(chapter 3, below)","cites":[3,4,5,6,7]}
{"seq":9,"world":"machine","kind":"reflex","body":{"percept":{"node":"1","offered":["first","second"]},"heading":4,"picked":"first","bits":0},"cites":[4,7]}
{"seq":10,"world":"machine","kind":"key","body":{"skill":"traverse-tale","seed":"reader","path":["1","4"]},"cites":[9]}
```

Each further incipit adds five events: reflex (or choice), key, text, hinge, chapter.

| Incipit | Node | Arrives by | Event | Bits | Hinge title |
|---|---|---|---|---|---|
| 1 | 1 | heading engaged | `e4` | 6.375 | *If you take your hands from the page,* |
| 2 | 4 | reflex, first | `e9` | 0 | *the book goes on* |
| 3 | 9 | reflex, first | `e14` | 0 | *by a table you wrote* |
| 4 | 5 | reflex, first | `e19` | 0 | *before you knew the way,* |
| 5 | 6 | reflex, first | `e24` | 0 | *and it answers each fork* |
| 6 | 11 | reflex, first | `e29` | 0 | *the same each time it comes,* |
| 7 | 11 | reflex, first (self-loop) | `e34` | 0 | *round and round one verse,* |
| 8 | 12 | take `e39`, then choice second `e40` | `e39`, `e40` | 1 + 1 | *until you take it back* |
| 9 | 15 | choice first | `e45` | 1 | *and choose where it ends.* |
| 10 | 21 | choice second (terminal) | `e50` | 1 | — |

The frame closes at chapter 21 (`e53`): incipit 10 is both a terminal and the tenth. Edge total: `1.585 + 6.375 + 1 + 1 + 1 + 1 = 11.960` bits, of which the heading is 6.375; six reflexes, 0 bits each.

**Chapter 3** (`e8`)

> You have taken your hands off the book [e3]. Before you did, you wrote it a table: nineteen answers, one for each fork in the tale, and every one of them says the first way [e4]. Nineteen answers look like nineteen bits. They are worth a little over six, because in ten openings the tale can go only eighty-three different ways under any table at all, and the book is counting what it can actually do [e4]. The page you have just read is the first verse of Genesis, set at the first node of a tale whose shape belongs to Queneau and whose words do not [e5][e6]. It ends at a fork [e7]. From here a small machine will answer for you. It will look at the fork, find the node's digit in your table, and take that way. It will not look at anything else; it does not remember the page before this one, and it will not wonder what you would have wanted. Perhaps you would like to watch it work. Each way it takes will cost you nothing, because you have already paid. If at any fork you would rather steer, say so, and it will let go at once. If you say nothing, it holds.

**Chapter 15** (`e38`)

> You are reading the same verse twice: the earth bringing forth grass, and the fruit tree yielding fruit after his kind [e31][e36]. The machine brought you back to it because your table says the first way at node eleven, and the first way at node eleven is node eleven [e4][e34]. It has no memory, so it cannot know that it has been here. It will answer this fork the same way next time and every time after. Nothing in it can stop the circling; only the frame can, and the frame has counted seven openings, with three left [e36]. None of the six ways it has taken has cost you a bit [e9][e14][e19][e24][e29][e34]. You may take the controls now. If you say nothing, it holds.

The Reader takes (`e39`) and picks the second way (`e40`). Chapter 17 says so, names the take as worth one bit, and offers node 12 by hand.

## Replay

```text { type: notebook.cell }
replay-flight(trace):
    for each reflex r:
        h := the hinge r cites; H := the heading r cites
        r.percept = { node: h.node, offered: ids of h.offered }           # P1, stateful
        r.picked  = r.percept.offered[ H.picked[h.node − 1] ]             # P3, T13
        r.bits    = 0                                                     # P7, T5
        no Reader choice cites h before r                                 # P6, unengaged
        no take cites H before r                                          # P2, unengaged
        h is a non-terminal traverse-tale hinge                           # P5, unengaged
    H.bits = log2(frame.json autopilot.flights[incipits left at H])       # T5
```

Shuffling the order of the earlier events, or deleting every text, must not change any reflex. If it does, the autopilot read something it may not (`stateful`).

## Citations

| Pin | Supports |
|---|---|
| [Hintze (2016)](https://theconversation.com/understanding-the-four-types-of-ai-from-reactive-robots-to-self-aware-beings-67616) | the reactive machine the autopilot is |
| [`Annex4/Conte2.py` at `ef9ef32`](https://github.com/Princeton-CDH/digital-oulipo/blob/ef9ef320457837df8028188117a8568a56f796bb/Annex4/Conte2.py) | the graph it flies |
| [Autopilot](https://en.wikipedia.org/wiki/Autopilot) | hold a set heading; give control back to the pilot |
| [*If on a winter's night a traveler*](https://en.wikipedia.org/wiki/If_on_a_winter%27s_night_a_traveler) | the frame that keeps alternating around it |

## Falsification

| Challenge | Failure signal |
|---|---|
| Emit a reflex before any heading | `unengaged` |
| Fly the `s7-poe` hinge, or the birthday value | `unengaged` |
| Pick second at node 11 on the second visit "to break the loop" | `stateful` |
| Keep flying after a `take` | `unengaged` |
| Chapter 15 says "You chose the first way again" | `invention` |
| Report the heading as 19 bits, or a reflex as 1 | T5 |
| Two incipits with only a key between them, no reflex or choice | `closed_loop` |

## Licence

MIT, see `LICENSE`.

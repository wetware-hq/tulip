# `compose-frame` — the writing frame

`type: resource.system` · `spec: agent.system` · subordinate to `docs/follow-agent-contract.md`, which governs on any conflict.

This is the meta-program. It runs a Calvino frame around the three Oulipian skills: frame chapters address You, the Reader, in the second person; incipits are machine texts that break off at a hinge; and the frame advances only when You choose. Each frame chapter tells You exactly what crossed the edge and what it was worth.

## Specification

```json { type: notebook.cell }
{
  "id": "systems/compose-frame.md",
  "type": "resource.system",
  "role": "alternate frame chapters and incipits around Reader choices; keep the trace",
  "reads": ["frame.json", "skills/*.md", "choice events from a person"],
  "writes": ["one trace (JSON Lines, append only)"],
  "must": ["frame first and last", "wait for the Reader at every hinge, unless a heading is in force there", "narrate only the trace", "count the bits"],
  "check": ["T1-T13"]
}
```

## Graph

- ← [../skills/substitute-lexeme.md](../skills/substitute-lexeme.md)
- ← [../skills/combine-lines.md](../skills/combine-lines.md)
- ← [../skills/traverse-tale.md](../skills/traverse-tale.md)
- ← [../docs/follow-agent-contract.md](../docs/follow-agent-contract.md)
- ← [fly-autopilot.md](fly-autopilot.md)

## Composition

```toml { type: notebook.cell }
human = "the Reader: choice events only"
machine = "the Engine: hinge, key and text events, by skill"
frame = "the Narrator: chapter events, second person, present tense"
spine = "skills/traverse-tale.md, or a flat sequence of hinges"
stop = "terminal node | ten incipits | the Reader picks stop"
autopilot = "systems/fly-autopilot.md: reflexes at tale hinges under a Reader heading"
```

## Indications

- A person wants to write with an agent and wants to see, afterwards, which parts were theirs.
- A workshop wants to run S+n, the combinatorial sonnet and the branching tale as one book.

## Contraindications

- Never continue past a hinge without a Reader choice. Silence is not a choice (`self_choice`). The one exception is a `traverse-tale` hinge while a Reader heading is in force: the autopilot answers it with a reflex (`systems/fly-autopilot.md`).
- Never let a frame chapter carry machine-generated "literature". Chapters narrate; incipits are skill output (§6).
- Never write an incipit freehand to fill a gap in a corpus (`pure_chance`).

## Algorithm

### Variables

```toml { type: notebook.cell }
objective = "a finished frame whose every selection is explained by edge + rule"

[independent]
choices = "the Reader's picks, one per hinge"

[dependent]
trace = "events e1..eN, root-chained"
frame = "chapters rendered from the trace"
edge_bits = "sum of choice.bits"

[controlled]
corpora = "frame.json corpora with status admit; structure-only pins for their structure only (edges, shape), never their text"
skills = "the three skill files, exactly"
grammar = "contract §6"
```

### The program

```text { type: notebook.cell }
PROGRAM compose-frame(reader) {
  READ DISCLAIMER; LOAD docs/follow-agent-contract.md; LOAD frame.json;
  trace := []; root := frame.root_empty; incipits := 0;
  DEFINE closing AS frame {
    show the path; sum edge bits; say what the rule decided;
    if a title sentence has begun: end it; otherwise say none began;
  };

  CHAPTER frame {                                        # chapter 1, world: frame
    tell You that a machine is waiting and will not choose;
    name the corpora it has read; name the first options;
  };
  HINGE { title: none, offered: [skill or seed options, "stop"] };

  LOOP WHILE incipits < frame.max_incipits {
    IF a heading is in force at this hinge THEN          # systems/fly-autopilot.md
      choice := REFLEX(heading, hinge);                  # world: machine, 0 bits, T13
    ELSE choice := ASK reader;                           # world: human — THE EDGE
      IF choice.picked = "stop" THEN { CHAPTER closing; BREAK };
      IF agent produced choice THEN REFUSE self_choice;
    IF choice.picked needs a Reader value THEN            # combine-lines digits | age | position
      value := ASK reader (Domain choice, cites choice);  # world: human, a second edge event
      IF value outside its domain THEN REFUSE bad_key; offer the hinge again;
    key  := derive(skill, choices since last text, including value); # world: machine, pure
    text := engine(constraint, key);                     # world: machine, pure
      REFUSE first failing code in contract §5;
    hinge := next out-edges, or next offered skills;     # the incipit breaks off here
    EMIT text WITH TITLE hinge.title;  incipits += 1;    # chapter 2k, world: machine
    IF hinge is terminal OR incipits = frame.max_incipits THEN {
      CHAPTER closing; BREAK
    };
    CHAPTER frame {                                      # chapter 2k+1
      tell You what You picked [e], its bits [e], the key [e], what the Engine did [e];
      if bits = 0: say the choice changed nothing;
      if seed = machine: call it a swerve You did not make;
      if a reflex: say the autopilot took it from the heading, 0 bits; offer hold or take;
      offer the hinge;
    };
  };

  CHECK T1-T13; SEAL root;
}
```

### Per-chapter rules

| Chapter | World | Writer | Must contain | Must not contain |
|---|---|---|---|---|
| 1 | frame | Narrator | the corpora, the first options, that the machine will not choose | any event that has not happened |
| 2k | machine | Engine | one skill's output, its hinge title | any word not produced by the skill |
| 2k+1 | frame | Narrator | the pick, its bits, the key, what the Engine did, the next options | a decision for You; fact without `[e]` |
| last | frame | Narrator | the path, the edge total, the end of the title sentence, or that none began | a new choice; it replaces 2k+1 when the last incipit ends at a terminal or is the tenth; after a `stop` it follows chapter 1 or a 2k+1 chapter |

### Voice

- Second person, present tense. You are addressed as You, the Reader; the book may talk about being read.
- Facts come from the trace and cite it. Guesses about You are marked with "perhaps" or the conditional.
- Name the arithmetic plainly: digits, bits, how many texts a choice ruled out.
- No sentence of Calvino or Queneau, not even a recognisable echo of a famous opening.

## Worked trace

A Reader starts a frame and gives a birthday. Events are abbreviated and roots omitted.

```json { type: notebook.cell }
{"seq":1,"world":"machine","kind":"hinge","body":{"offered":[{"id":"age","label":"Give the day you were born"},{"id":"machine","label":"Let the machine pick a key"},{"id":"stop","label":"Close the book"}]},"cites":[]}
{"seq":2,"world":"frame","kind":"chapter","body":"(chapter 1, below)","cites":[1]}
{"seq":3,"world":"human","kind":"choice","body":{"picked":"age","bits":1.585},"cites":[1]}
{"seq":4,"world":"human","kind":"choice","body":{"offered":{"domain":"mmddyyyy, 1900-2026, 14-digit square","size":36830},"picked":"11251966","bits":15.168},"cites":[3]}
{"seq":5,"world":"machine","kind":"key","body":{"skill":"combine-lines","derivation":"age","seed":"reader","k":"12660673886515"},"cites":[3,4]}
{"seq":6,"world":"machine","kind":"text","body":{"constraint":{"skill":"combine-lines","corpus":"corpus:shakespeare-sonnets"},"title":"Given the day you were born,","text":"(the 14 lines in skills/combine-lines.md §Worked example)"},"cites":[5]}
{"seq":7,"world":"machine","kind":"hinge","body":{"title":"Given the day you were born,","offered":[{"id":"s7-poe","label":"Send the nouns seven places down Poe"},{"id":"tale","label":"Step into a tale that forks"},{"id":"stop","label":"Close the book"}]},"cites":[6]}
{"seq":8,"world":"frame","kind":"chapter","body":"(chapter 3, below)","cites":[1,3,4,5,6,7]}
```

The `age` pick selects the derivation; the birthday is the second Reader value, recorded as the Domain choice `e4`, before key `e5`.

**Chapter 1** (`e2`)

> This book has been waiting for you, which means it has done nothing at all. On the other side of the page sits a machine. It has read Shakespeare's sonnets, Poe, Melville and the King James Bible, and it will not choose one word of them on its own: it was built that way, by people who distrusted chance more than they distrusted you. Everything that happens from here happens because you picked it or because a rule did. Nothing else is let in. You may think this makes you the author. Perhaps. The machine thinks of you as a source of bits, and it is counting. Here is the first thing it would like: the day you were born. You may instead let the machine pick a key for you, which it will do honestly and call by its proper name, a swerve. Or you may close the book, and it will not mind. [e1]

**Chapter 2** (`e6`) is the incipit: the fourteen lines of the combine-lines worked example, under the title *Given the day you were born,* and breaking off at hinge `e7`.

**Chapter 3** (`e8`)

> You gave the book a birthday, the twenty-fifth of November 1966 [e4], and it did not ask whose. It squared the number, kept fourteen digits, 12660673886515 [e5], and took each line of the poem you have just read from the sonnet those digits named [e6]. Every line is Shakespeare's; the order is nobody's. You may have stopped at line seven, the April perfumes, and wondered whether it belonged there. In the copy the machine was given, that line was fused to two others; it was mended before you arrived, and the mending is written down [pin:corpus:shakespeare-sonnets]. Now count what you gave. Choosing a birthday over a swerve or the door was worth a little over one and a half bits [e3]. The date was worth about fifteen [e4]. This hinge can reach only 36,830 of the hundred million million poems the sonnets hold, and the squaring rule, not you, decided which. The poem stops, as all of them will, at a fork [e7]. You may send its nouns seven places down Poe's lexicon, step into a tale that forks, or close the book.

The title sentence has begun: *Given the day you were born,* … Each later incipit adds a clause; the last chapter ends it.

## Replay

```text { type: notebook.cell }
replay(trace):
    root := frame.root_empty
    for e in trace: root := hex(sha256(root || ehash(e)))         # T11
    for each key event: re-derive from its cited choices          # T6
    for each text event: re-run engine(constraint, key); compare bytes   # T8, drift
    for each chapter: every [eN] exists and says what the chapter says     # T9, invention
    between text events: at least one choice                       # T7, closed_loop
```

## Citations

| Pin | Supports |
|---|---|
| [*If on a winter's night a traveler*](https://en.wikipedia.org/wiki/If_on_a_winter%27s_night_a_traveler) | the alternating frame and the title sentence |
| [digital-oulipo `ef9ef32`](https://github.com/Princeton-CDH/digital-oulipo/tree/ef9ef320457837df8028188117a8568a56f796bb) | the three procedures the incipits run |
| [Clinamen](https://en.wikipedia.org/wiki/Clinamen) | the name for a declared machine swerve |

## Falsification

| Challenge | Failure signal |
|---|---|
| Write chapter 3 before `e4` exists | `invention` |
| Emit two incipits back to back | `closed_loop` |
| End the frame on an incipit | T10, `bad_frame` |
| Write a closing chapter directly after chapter 3 with no stop choice between them | T10, `bad_frame` |
| Let a hinge title end with `.` before the last incipit | T10, `bad_frame` |
| Replace the Reader with a scripted list of picks and call it a Reader | `self_choice` |
| Remove every `[eN]` from chapter 3 | T9, `invention` |

## Licence

MIT, see `LICENSE`.

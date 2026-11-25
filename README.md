# tulip 🌷

**A cybernetic writing engine: a person chooses, a machine follows rules, and the book counts who did what.**

> tulip is academic and literary research material. Its texts are built from cited public-domain sources and are not a licence to reproduce any copyrighted work. (Full text: [`DISCLAIMER`](DISCLAIMER).)

## What is this?

tulip is a set of documents that tell an AI agent how to write a book *with* a person, under strict rules, so that afterwards you can see exactly which parts came from the person and which came from the machine.

It is a **specbase**: there is no app and no code. An agent reads these files and follows them.

## The idea: one narrow edge

Picture two worlds with a thin wall between them.

- **The human world.** You, the Reader. You make choices: a number, a date, a fork in a story.
- **The machine world.** The Engine. It applies a fixed rule to a fixed text. It never picks anything by itself, and it never makes things up.

The only thing allowed through the wall is **your choice**. Every word in the result is there either because you chose it or because a rule put it there. Each choice is measured in **bits**, so the book can tell you exactly how much of it was yours. A choice between two options that lead to the same place is worth 0 bits, and the book will say so.

This is a **feedback loop**: you choose, the machine writes, you read and choose again. The loop must pass through you every time; the machine is never allowed to feed its own output back to itself.

## Where the rules come from

tulip takes its machinery from the [Digital Oulipo](https://github.com/Princeton-CDH/digital-oulipo) project at Princeton, which turned three famous procedures of the [Oulipo](https://oulipo.net) (a French workshop of writers and mathematicians) into programs:

| Procedure | Who | What it does | tulip file |
|---|---|---|---|
| **S+7** | Jean Lescure | Replace every noun with the noun seven entries later in a dictionary. | [`skills/substitute-lexeme.md`](skills/substitute-lexeme.md) |
| **A hundred thousand billion poems** | Raymond Queneau | Ten sonnets; pick any line from any sonnet, line by line. That makes 10^14 poems. | [`skills/combine-lines.md`](skills/combine-lines.md) |
| **A tale your way** | Raymond Queneau | A short story as a map of yes/no choices. | [`skills/traverse-tale.md`](skills/traverse-tale.md) |

tulip uses only public-domain texts (Shakespeare, Poe, Melville, the King James Bible and others). Queneau's own poems and story are still under copyright, so tulip uses only their *shape*, never their words.

## The frame: after Calvino

[Italo Calvino](https://en.wikipedia.org/wiki/Italo_Calvino), an Oulipo member, wrote *[If on a winter's night a traveler](https://en.wikipedia.org/wiki/If_on_a_winter%27s_night_a_traveler)*. It alternates chapters about "you, the Reader" with the first chapters of ten different novels, each of which breaks off just as it gets going.

tulip borrows that structure (and none of Calvino's words):

1. A **frame chapter** speaks to you directly and tells you what is happening.
2. An **incipit** (an opening) is written by the machine using one of the three procedures. It stops at a fork.
3. You choose. The next frame chapter tells you what you chose, how many bits it was worth, and what the machine did with it.

The titles of the incipits, read in order, form one sentence. The book ends when the story reaches an ending, after ten incipits, or when you close it.

The full program is in [`systems/compose-frame.md`](systems/compose-frame.md), with a worked example.

## A small example

You give the book your birthday, 25 November 1966. The book squares `11251966`, keeps the first fourteen digits, `12660673886515`, and uses digit *i* to pick line *i* from one of ten Shakespeare sonnets:

```text
Lo! in the orient when the gracious light
Who for thy self art so unprovident.
The argument all bare is of more worth
Than when it hath my added praise beside!
…
```

Then it tells you that your date was worth about 15 bits, that this kind of question can reach only 36,830 of the 10^14 possible poems, and that the squaring rule, not you, decided which.

## What is in this repository

| Path | What it covers |
|---|---|
| [`DISCLAIMER`](DISCLAIMER) | The one-line research-use notice. Agents read it first. |
| [`docs/read-repository.md`](docs/read-repository.md) | Where to start: which file to read for which task. |
| [`docs/follow-agent-contract.md`](docs/follow-agent-contract.md) | The rulebook. It wins over every other file if they disagree. |
| [`docs/pin-sources.md`](docs/pin-sources.md) | Which source texts may be used, and the exact version of each. |
| [`skills/`](skills) | The three procedures. |
| [`systems/compose-frame.md`](systems/compose-frame.md) | The writing frame that runs them with a Reader. |
| [`frame.json`](frame.json) | The pinned sources and frame settings. |
| [`ooc/tulip.og`](ooc/tulip.og) | A compact map of the files, for agents that follow links. |

**Agents** should start at `DISCLAIMER`, then [`docs/read-repository.md`](docs/read-repository.md). This README is a human overview only; where it differs from the contract, the contract is correct.

## Current status

- This is a specification. Nothing runs yet.
- The source project has no licence, so nothing from it is copied here; it is cited and pinned by commit and hash.
- The Shakespeare text in the source has a typing error that merges three lines into one; tulip records a repair (see [`docs/pin-sources.md`](docs/pin-sources.md)).
- If you edit `ooc/tulip.og`, recompute its checksum: sha256 of the file without its `checksum:` line.

## Glossary

| Term | Meaning |
|---|---|
| **Reader** | The person. The only one allowed to choose. |
| **Engine** | The machine following one procedure exactly. |
| **Narrator** | The agent writing the frame chapters. It may only report what happened. |
| **Hinge** | The fork where an incipit stops and you choose. |
| **Bit** | The unit of choice. Picking one of two real options is 1 bit; one of 36,830 is about 15. |
| **Trace** | The append-only record of every choice and every text. |
| **Clinamen** | A swerve: a key the machine picked at random, declared as such. |

## Licence

MIT. See [`LICENSE`](LICENSE).

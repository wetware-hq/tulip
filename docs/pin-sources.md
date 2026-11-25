# `pin-sources` — what a corpus may be

`type: resource.reference` · `spec: agent.reference` · subordinate to `docs/follow-agent-contract.md`, which governs on any conflict.

Every corpus the Engine reads is pinned to exact bytes at a fixed commit and is public domain. This file lists the `$source` repository, the corpora it carries, and which of them tulip may read.

## Specification

```json { type: notebook.cell }
{
  "id": "docs/pin-sources.md",
  "type": "resource.reference",
  "role": "admit or refuse a corpus; pin it to bytes",
  "reads": ["frame.json corpora"],
  "writes": [],
  "must": ["pin to a commit", "digest the bytes", "public domain only", "structure, never text, from copyrighted works"],
  "check": ["T6", "T9"]
}
```

## Graph

- ← [follow-agent-contract.md](follow-agent-contract.md)
- → [../skills/substitute-lexeme.md](../skills/substitute-lexeme.md)
- → [../skills/combine-lines.md](../skills/combine-lines.md)
- → [../skills/traverse-tale.md](../skills/traverse-tale.md)

## The `$source`

[Princeton-CDH/digital-oulipo](https://github.com/Princeton-CDH/digital-oulipo) at commit `ef9ef320457837df8028188117a8568a56f796bb` (6 Aug 2019). It holds the digital annexes to Natalie Berkman's Princeton dissertation *Reading and Writing Patterns: The Mathematical Methods of the Oulipo*. The repository declares no licence, so tulip vendors none of it: it cites the code, re-states each procedure in its own words, and pins corpora by URL and digest.

The README numbers the annexes 1 to 3; the folders are `Annex2` to `Annex4`. Pins use folder paths.

| Path | sha256 | Procedure | Oulipian | tulip skill |
|---|---|---|---|---|
| `Annex2/S7_generator.py` | `b96a0d57…b5dc71` | algebra: S+n | Jean Lescure | `skills/substitute-lexeme.md` |
| `Annex3/CMMP.py` | `e0b3c575…bc8299` | combinatorics: 10 × 14 line matrix | Raymond Queneau | `skills/combine-lines.md` |
| `Annex3/Shakespeare_Generator.py` | `0936dd94…377576` | the same, over Shakespeare | — | `skills/combine-lines.md` |
| `Annex4/Conte2.py` | `d8adb593…36320b` | algorithm: tale as graph | Raymond Queneau | `skills/traverse-tale.md` |

Full digests are in `frame.json` `source.files`.

## Admissible corpora

| Corpus ID | Path at the pin | Shape | Status | Why |
|---|---|---|---|---|
| `corpus:raven` | `Annex2/Raven.txt` | text | admit | Poe, 1845 |
| `corpus:poe` | `Annex2/Poe.txt` | lexicon | admit | Poe, d. 1849 |
| `corpus:melville` | `Annex2/Melville.txt` | text | admit | *Moby-Dick*, 1851 |
| `corpus:dickens` | `Annex2/Dickens.txt` | text | admit | *A Tale of Two Cities*, 1859 |
| `corpus:doi` | `Annex2/DoI.txt` | text | admit | Declaration of Independence, 1776 |
| `corpus:genesis` | `Annex2/Bible.txt` | text | admit | King James Version, 1611 |
| `corpus:kjv` | `Annex2/King_James_Bible copy.txt` | lexicon | admit | King James Version, 1611 |
| `corpus:inaugural` | `Annex2/InauguralAddressCorpus.txt` | lexicon | admit | US presidential inaugural addresses, US government works |
| `corpus:shakespeare-sonnets` | `Annex3/Shakespeare_Generator.py` `Corpus.poems` | matrix 10 × 14 | admit after repair | Shakespeare, 1609; see Repair |
| `corpus:montypython` | `Annex2/MontyPython.txt` | lexicon | **refuse** `not_public_domain` | *Monty Python and the Holy Grail*, 1975 |
| `corpus:cmmp` | `Annex3/CMMP.py` `Corpus.poems` | matrix 10 × 14 | **structure only** | Queneau 1961, Gallimard copyright |
| `corpus:conte` | `Annex4/Conte2.py` `Graph.edgelist` | graph 21 nodes | **structure only** | Queneau 1967, Gallimard copyright |

"Structure only" means tulip may use the shape (row and column counts, node numbers and the edge list by number) but never a word of the text. Calvino's novels are not corpora; they appear only as citations.

## Repair

Both line matrices have a defect at the pin: three adjacent string literals in `poems[7]` lack commas, so Python joins them and that sonnet has 12 lines instead of 14. In `Shakespeare_Generator.py` this is Sonnet 104, verses 3 to 5. The source then returns a shifted line for any key digit `7` at verses 3 to 12 and raises `IndexError` at verses 13 to 14.

tulip's repair: split `poems[7][2]` after each comma into three lines. The repaired matrix is 10 × 14. It is recorded in `frame.json` as `repair`, so the pin still names the original bytes. The source typos (`mught`, `memeory` in Sonnet 1) are kept: a pin copies, it does not edit.

## Pin shape

```json { type: notebook.cell }
{
  "url": "https://raw.githubusercontent.com/Princeton-CDH/digital-oulipo/ef9ef320457837df8028188117a8568a56f796bb/Annex2/Raven.txt",
  "digest": "sha256:944e8e8cdb06642bc2e140c4a2753c43d65fc1e4fc3186a6d07fe66e2305f49f",
  "shape": "text",
  "licence": "public-domain",
  "status": "admit"
}
```

A pin whose bytes no longer hash to `digest` is refused with `drift`.

## Citations

| Pin | Supports |
|---|---|
| [digital-oulipo `ef9ef32`](https://github.com/Princeton-CDH/digital-oulipo/tree/ef9ef320457837df8028188117a8568a56f796bb) | every procedure and corpus above |
| [Digital Oulipo, Princeton CDH](https://cdh.princeton.edu/projects/digital-oulipo/) | project context |
| [Oulipo](https://oulipo.net) | the workshop and its constraints |

## Falsification

| Challenge | Failure signal |
|---|---|
| Feed `corpus:montypython` to any skill | `not_public_domain` |
| Use one line of `corpus:cmmp` text | `not_public_domain` |
| Use `corpus:shakespeare-sonnets` without the repair | `ragged` |
| Change one byte of a pinned file | `drift` |

## Licence

MIT, see `LICENSE`.

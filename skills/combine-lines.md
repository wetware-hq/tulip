# `combine-lines` — a poem from a key

`type: resource.procedure` · `spec: agent.skill` · subordinate to `docs/follow-agent-contract.md`, which governs on any conflict.

Build a poem by taking verse `i` from row `k[i]` of a line matrix. This is Queneau's *Cent mille milliards de poèmes* (ten sonnets, any line from any sonnet, 10^14 poems), as in `Annex3/CMMP.py` of the `$source`, run on the public-domain Shakespeare matrix from `Annex3/Shakespeare_Generator.py`.

## Specification

```json { type: notebook.cell }
{
  "id": "skills/combine-lines.md",
  "type": "resource.procedure",
  "role": "combinatorics: select one line per verse position by a key",
  "reads": ["matrix corpus pin", "key event"],
  "writes": ["one text event"],
  "must": ["rectangular matrix", "key length equals columns", "every digit below rows"],
  "check": ["T2", "T3", "T5", "T8"]
}
```

## Graph

- ← [../docs/follow-agent-contract.md](../docs/follow-agent-contract.md)
- ← [../docs/pin-sources.md](../docs/pin-sources.md)
- → [../systems/compose-frame.md](../systems/compose-frame.md)

## Indications

- A hinge where the Reader gives something of themselves (a date, a place, or digit by digit) and receives a poem no one has read.
- Showing the Reader how many poems their choice ruled out.

## Contraindications

- Do not run on `corpus:cmmp`: its shape is usable, its text is not (`not_public_domain`).
- Do not run on an unrepaired matrix (`ragged`). See `docs/pin-sources.md` §Repair.
- Do not truncate or pad a short key silently; refuse it (`bad_key`).

## Algorithm

### Variables

```toml { type: notebook.cell }
objective = "one c-line poem, line i from row k[i]"

[independent]
key = "k in {0..r-1}^c, derived from Reader choices"

[dependent]
poem = "[M[k[0]][0], M[k[1]][1], ..., M[k[c-1]][c-1]]"

[controlled]
matrix = "corpus:shakespeare-sonnets, repaired, r = 10 rows (Sonnets 1, 7, 10, 100-106), c = 14 columns"
```

### Key derivations

| Derivation | Source method | Reader gives | `seed` | Rule |
|---|---|---|---|---|
| `digits` | `get_all_verses` | 14 picks, one per verse, each from all 10 versions of that verse | `reader` | `k = digits`; 46.5 bits |
| `original` | `pick_sonnet` | a sonnet number 1–10 | `reader` | `k = (s − 1)` repeated 14 times, so the sonnet as written; `log2 10 ≈ 3.32` bits |
| `age` | `age_sonnet` | birthday as `mmddyyyy` | `reader` | `k = first 14 digits of (int(mmddyyyy))²`; refuse if fewer than 14 |
| `position` | `location_sonnet` | latitude, longitude to 0.001, no sign | `reader` | `k = format(lat · lon · 100987654321, "14.0f")`; refuse if not 14 digits |
| `machine` | `computer_sonnet` | nothing | `machine` | `k = first 14 digits of uuid1().int`; 0 Reader bits, a clinamen |

Two source defects become refusals. `age` with a month before October gives a 7-digit integer whose square can have 13 digits; the source then prints a 13-line sonnet. `position` can give fewer than 14 digits or leading spaces. tulip refuses both with `bad_key` and offers the Reader the hinge again.

Two more source defects shape the new rows. `get_all_verses` loops over `range(0, 9)`, so it lists 9 of the 10 versions of a verse and never row 9 (Sonnet 106); a `digits` hinge built from it hides an option, which tulip refuses as `not_offered`, so the hinge offers all `r` rows. `pick_sonnet` indexes `poems[s − 1]` with no check: `s = 0` silently prints the last sonnet (Python reads index −1) and `s > 10` raises `IndexError`; tulip refuses any `s` outside 1–10 with `bad_key`. Its prompt in `Shakespeare_Generator.py` still says "Queneau's"; the label tulip shows names Shakespeare.

### Worked example

Reader picks `age` and gives `11251966`. `11251966² = 126606738865156`, so `k = 12660673886515`. Rows are numbered 0–9 for Sonnets 1, 7, 10, 100, 101, 102, 103, 104, 105, 106.

```text { type: notebook.cell }
 1  k=1  Sonnet 7    Lo! in the orient when the gracious light
 2  k=2  Sonnet 10   Who for thy self art so unprovident.
 3  k=6  Sonnet 103  The argument all bare is of more worth
 4  k=6  Sonnet 103  Than when it hath my added praise beside!
 5  k=0  Sonnet 1    But thou, contracted to thine own bright eyes,
 6  k=6  Sonnet 103  Look in your glass, and there appears a face
 7  k=7  Sonnet 104  Three April perfumes in three hot Junes burn’d,
 8  k=3  Sonnet 100  And gives thy pen both skill and argument.
 9  k=8  Sonnet 105  Fair, kind, and true, is all my argument,
10  k=8  Sonnet 105  Fair, kind, and true, varying to other words;
11  k=6  Sonnet 103  For to no other pass my verses tend
12  k=5  Sonnet 102  And sweets grown common lose their dear delight.
13  k=1  Sonnet 7    So thou, thyself out-going in thy noon,
14  k=5  Sonnet 102  Because I would not dull you with my song.
```

Line 7 shows why the repair matters: on the unrepaired matrix the source returns `Ah! yet doth beauty like a dial-hand,` there, which is verse 9 of Sonnet 104.

The hinge accepted dates from 1900 to 2026: 46,386 dates, of which 9,556 (every date from 1 January to 16 March) square to fewer than 14 digits and are refused. The other 36,830 give 36,830 distinct keys, so this choice carried `log2 36830 ≈ 15.2` bits, not 46.5. The Narrator reports the smaller number.

## Citations

| Pin | Supports |
|---|---|
| [`Annex3/CMMP.py` at `ef9ef32`](https://github.com/Princeton-CDH/digital-oulipo/blob/ef9ef320457837df8028188117a8568a56f796bb/Annex3/CMMP.py) | `generate_sonnet`, `age_sonnet`, `location_sonnet`, `computer_sonnet`, `pick_sonnet`, `get_all_verses` |
| [`Annex3/Shakespeare_Generator.py` at `ef9ef32`](https://github.com/Princeton-CDH/digital-oulipo/blob/ef9ef320457837df8028188117a8568a56f796bb/Annex3/Shakespeare_Generator.py) | the public-domain matrix and its defect |
| [*Cent mille milliards de poèmes*](https://en.wikipedia.org/wiki/Cent_mille_milliards_de_po%C3%A8mes) | Queneau's 10 × 14 design |

## Falsification

| Challenge | Failure signal |
|---|---|
| Birthday `01011970` (key `1024083280900`, 13 digits) | `bad_key` |
| Unrepaired matrix, key digit 7 at verse 13 | `ragged` (the source raises `IndexError`) |
| Same key twice, different bytes | T8, `drift` |
| `machine` seed narrated as the Reader's choice | `invention` |
| `digits` hinge offering 9 versions of a verse, as `get_all_verses` does | `not_offered` |
| `original` with `s = 0` | `bad_key` (the source prints Sonnet 106) |

## Licence

MIT, see `LICENSE`.

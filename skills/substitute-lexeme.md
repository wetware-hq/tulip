# `substitute-lexeme` — S+n

`type: resource.procedure` · `spec: agent.skill` · subordinate to `docs/follow-agent-contract.md`, which governs on any conflict.

Replace every noun of a text with the noun `n` places later in a sorted lexicon. This is Jean Lescure's S+7, generalised to any offset and any admitted lexicon, as in `Annex2/S7_generator.py` of the `$source`. The Reader picks `n` and the lexicon; the Engine does the rest.

## Specification

```json { type: notebook.cell }
{
  "id": "skills/substitute-lexeme.md",
  "type": "resource.procedure",
  "role": "algebra: shift each noun by a fixed offset in a lexicon",
  "reads": ["text corpus pin", "lexicon corpus pin", "offset choice"],
  "writes": ["one text event"],
  "must": ["pinned tagger", "codepoint order", "clamp at the end", "nouns only"],
  "check": ["T1", "T3", "T6", "T8"]
}
```

## Graph

- ← [../docs/follow-agent-contract.md](../docs/follow-agent-contract.md)
- ← [../docs/pin-sources.md](../docs/pin-sources.md)
- → [../systems/compose-frame.md](../systems/compose-frame.md)

## Indications

- A hinge that offers the Reader an author to read through (the lexicon) and a distance to travel (`n`).
- A second pass over another skill's output, to make a familiar text strange.

## Contraindications

- Do not use a refused or structure-only corpus as text or lexicon (`not_public_domain`).
- Do not let the tagger vary between runs; an unpinned tagger breaks T8 (`drift`).
- Do not substitute verbs, adjectives or names chosen by taste. The rule picks the words, not the agent (`pure_chance`).

## Algorithm

### Variables

```toml { type: notebook.cell }
objective = "a text in which only nouns moved, each by exactly n"

[independent]
lexicon = "corpus id, picked by the Reader from the offered admitted lexicons"
n = "integer offset, picked by the Reader; the source asks 'For an S+n, what would you like n to be?'"

[dependent]
text = "the substituted text"

[controlled]
source = "text corpus pin"
tagger = "one pinned part-of-speech tagger and model (the source uses NLTK pos_tag)"
order = "Unicode codepoint order, as Python sorted(set(...))"
```

### Process

```text { type: notebook.cell }
L := sorted, de-duplicated nouns (NN, NNS, NNP, NNPS) of the lexicon corpus
for each token w of the source text, in order:
    if tag(w) in {NN, NNP}:     add w to L (keep sorted);   emit L[min(idx(w) + n, |L| − 1)]
    if tag(w) in {NNS, NNPS}:   add lemma(w) to L;          emit plural(L[min(idx(lemma) + n, |L| − 1)])
    otherwise:                   emit w
```

- The source adds each text noun to the lexicon before shifting, so a noun always has an index. tulip keeps this.
- The source clamps at the last entry instead of wrapping round. tulip keeps the clamp and records it in the `text` event, because a clamped noun is a selection made by the rule, not by `n`.
- The source lower-cases and drops non-alphabetic tokens. tulip emits them unchanged, so punctuation and line breaks survive.
- Key space: `1 ≤ n < |L|`. Offered as a choice, `bits = log2(number of offered offsets)`; the classic offer is the single value 7, worth 0 bits.

## Citations

| Pin | Supports |
|---|---|
| [`Annex2/S7_generator.py` at `ef9ef32`](https://github.com/Princeton-CDH/digital-oulipo/blob/ef9ef320457837df8028188117a8568a56f796bb/Annex2/S7_generator.py) | lexicon build, offset, clamp, plural handling |
| [S+7, Oulipo](https://oulipo.net) | Lescure's method |
| [NLTK `pos_tag`](https://www.nltk.org/api/nltk.tag.html) | the source's tagger |

## Falsification

| Challenge | Failure signal |
|---|---|
| Run twice with different tagger models | T8, `drift` |
| Pick `n = 0` or `n ≥ \|L\|` | `bad_key` |
| Use `corpus:montypython` as lexicon | `not_public_domain` |
| Report 1 bit when only `n = 7` was offered | T5 |

## Licence

MIT, see `LICENSE`.

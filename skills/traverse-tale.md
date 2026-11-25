# `traverse-tale` — walk a branching tale

`type: resource.procedure` · `spec: agent.skill` · subordinate to `docs/follow-agent-contract.md`, which governs on any conflict.

Treat a tale as a directed graph whose nodes are passages and whose edges are the Reader's options, and walk it one choice at a time. This is Queneau's *Un conte à votre façon*, as in `Annex4/Conte2.py` of the `$source`. `corpus:conte` is structure-only: that status allows the graph's edges and shape, never its text. tulip fills its nodes with public-domain passages.

## Specification

```json { type: notebook.cell }
{
  "id": "skills/traverse-tale.md",
  "type": "resource.procedure",
  "role": "algorithm: offer out-edges, follow the pick, print the path",
  "reads": ["graph", "node fillings", "choice events"],
  "writes": ["one text event per visited node", "one hinge event per non-terminal node"],
  "must": ["offer every out-edge", "report 0 bits for illusory choices", "end at a terminal or a stop"],
  "check": ["T4", "T5", "T7", "T10"]
}
```

## Graph

- ← [../docs/follow-agent-contract.md](../docs/follow-agent-contract.md)
- ← [../docs/pin-sources.md](../docs/pin-sources.md)
- → [../systems/compose-frame.md](../systems/compose-frame.md)

## Indications

- The spine of a frame: each node is an incipit, each out-edge set is a hinge.
- Showing the Reader the path they took against every path they did not.

## Contraindications

- Do not fill a node with Queneau's text (`not_public_domain`).
- Do not walk a graph with no fill record (`unpinned`).
- Do not hide an out-edge or add one the graph does not have (`not_offered`).
- Do not report an illusory choice as a real one (T5).

## Algorithm

### Variables

```toml { type: notebook.cell }
objective = "a path from the start node to a terminal, chosen edge by edge"

[independent]
picks = "one option id per visited non-terminal node, from the Reader"

[dependent]
path = "list of visited node ids"
edge_bits = "sum of log2(distinct targets) over visited nodes"

[controlled]
graph = "corpus:conte structure, or another admitted graph"
start = "node 1"
fill = "frame.json fills[graph]: node n -> its pinned public-domain passage"
```

### Queneau's graph (structure only)

The edge list of `Graph.edgelist` at the pin, by node number. Nodes 20 and 21 are terminals.

```json { type: notebook.cell }
{
  "1": ["4", "2"], "2": ["16", "3"], "3": ["17", "21"], "4": ["9", "5"], "5": ["6", "7"],
  "6": ["11", "7"], "7": ["8", "10"], "8": ["7", "10"], "9": ["5", "21"], "10": ["11", "12"],
  "11": ["11", "12"], "12": ["15", "13"], "13": ["14", "14"], "14": ["15", "15"], "15": ["16", "21"],
  "16": ["21", "18"], "17": ["21", "18"], "18": ["19", "21"], "19": ["20", "21"]
}
```

| Feature | Nodes | What the Narrator must say |
|---|---|---|
| Illusory choice: both options lead to the same node | 13, 14 | your choice here carried 0 bits |
| Self-loop: one option returns to the same node | 11 | you may stay here as long as you like |
| Two-cycle | 7 ↔ 8 | you can go back and forth without moving on |
| Real choices | the other 17 non-terminals, 7, 8 and 11 included | 1 bit each; the graph holds 17 bits in all, and one walk collects only those of the nodes it visits |

### Process

Option labels come from `fills[graph].labels`, never from Queneau's option text.

```text { type: notebook.cell }
node := start; path := [start]
loop:
    emit text  (fill(node))
    if node is terminal:
        offer { again, stop }                       # the source asks whether to read another tale
        if pick = again: node := start; path += [start]; continue
        else: stop
    emit hinge (title, offered = out-edges of node)
    wait for choice; bits := log2(|distinct targets|)
    node := target(pick); path += [node]
at stop: emit text (the path, and the graph with the path marked)   # the source draws it with graphviz, path in green
```

## Citations

| Pin | Supports |
|---|---|
| [`Annex4/Conte2.py` at `ef9ef32`](https://github.com/Princeton-CDH/digital-oulipo/blob/ef9ef320457837df8028188117a8568a56f796bb/Annex4/Conte2.py) | graph, traversal, replay, path drawing |
| [*Un conte à votre façon*](https://fr.wikipedia.org/wiki/Un_conte_%C3%A0_votre_fa%C3%A7on) | Queneau's tale, 1967 |

## Falsification

| Challenge | Failure signal |
|---|---|
| Report 1 bit at node 13 | T5 |
| Offer only one of node 16's two edges | `not_offered` |
| Fill node 4 with Queneau's sentence | `not_public_domain` |
| Agent picks at node 1 because the Reader is slow | `self_choice` |
| Walk a graph with no fill record | `unpinned` |

## Licence

MIT, see `LICENSE`.

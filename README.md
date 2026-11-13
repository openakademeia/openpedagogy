# OpenPedagogy

An open protocol for AI-native teaching. A course author writes a book; any conforming tutor (an AI agent or a human) teaches from it, choosing among five techniques by the learner's level, generating exercises, and emitting a standard record. Created with the [openakademeia](https://github.com/openakademeia/openakademeia) initiative.

**Status:** `0.2.0-draft`, not for implementation. Read [PROTOCOL.md](PROTOCOL.md).

## What it defines

| Part | Covers |
|---|---|
| Course format | Any Markdown book (its `##` headings are the sections), plus optional exercises with answers |
| Tutor protocol | Session flow; learner level; five techniques (worked example, bridge, contrasting cases, predict and reveal, guided derivation); exercises; mastery; review; conduct |
| Learner record | The JSON attempt emitted per section |
| Conformance | Tutor evals |

The protocol's tutor section is self-contained, so a server or plugin can load it as an agent's instructions.

## Roadmap

- The tutor eval suite: scripted learners that check the protocol's observable behaviors.
- A first course (linear algebra) in the new format.

## License (proposed)

Text under CC BY 4.0; schemas and validators under MIT. Until the split is finalized, the repository is MIT ([LICENSE](LICENSE)).

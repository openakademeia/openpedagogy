# OpenPedagogy

An open specification for AI-native learning by prediction: the learner commits to a prediction with a confidence, sees it break, and gets the concept as the repair. Created with the [openacademy](https://github.com/slow42/openacademy) initiative.

**Status:** `0.1.0-draft`, not for implementation. Read [SPEC.md](SPEC.md).

## What it defines

| Layer | Spec | Covers |
|---|---|---|
| Principles | §4 | 13 learning-science claims, each graded A/B/C by evidence |
| Content format | §5–6 | Course → flight → lesson files, the lesson section grammar, the book, authoring rules |
| Tutor protocol | §7–8 | The commit → break → repair loop, branching on confidence × correctness, conduct and engagement limits |
| Learner record | §9 | The JSON attempt each lesson emits |

Each layer can be adopted on its own. Any conforming tutor (human or AI) can teach any conforming course.

## Roadmap

- JSON Schemas for `course.yaml` and the attempt record, plus a content validator (§12.2 gates).
- The protocol as a versioned document servers return over MCP (§10).
- `evidence/`: one file per principle, with its grade and last review date.
- Resolve the blocking open questions (§13): advancement and spaced review.

## License (proposed)

Spec text under CC BY 4.0; schemas and validators under MIT. Until the split is finalized, the repository is MIT ([LICENSE](LICENSE)).

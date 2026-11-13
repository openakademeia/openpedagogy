# OpenPedagogy Specification

**Version:** 0.1.0-draft · **Status:** draft, not for implementation · **Date:** 2026-11-04
**License (proposed):** spec text CC BY 4.0; schemas and validators MIT.

OpenPedagogy is an open specification for teaching by prediction. It defines how learning content is structured, how a tutor (human or AI agent) runs a lesson from that content, and what a lesson leaves behind as a record. Any tutor that implements the protocol can teach any course written in the format.

The key words MUST, MUST NOT, SHOULD, SHOULD NOT, and MAY are used as in RFC 2119.

---

## 1. Why this exists

When every learner has an LLM, delivering knowledge is worth almost nothing and running procedures is worth little. Judgment stays scarce: holding a mental model that predicts behavior, choosing the right question, catching a confident machine that is wrong. Explaining things to learners doesn't build judgment. Making them commit to a prediction, letting the prediction fail, and giving them the concept as the repair does.

Most AI tutors write their pedagogy into a private prompt. OpenPedagogy pulls it out so that:

- course authors write once against a stable format, and content works with any conforming tutor;
- tutors are judged against a published protocol instead of ad-hoc prompt behavior;
- practice claims are tied to evidence that can be checked, and contested claims are labeled as contested.

## 2. Scope

| In scope | Out of scope |
|---|---|
| Content model: course, flight, lesson, book section | Rendering, UI, typography |
| Lesson section grammar and authoring rules | Transport and API (MCP, HTTP); see §10 for one binding |
| Tutor protocol: the lesson loop, branching, dosage, conduct | Billing, tiers, identity |
| Attempt record: what a lesson must emit | Analytics, storage engines |
| Conformance levels and machine-checkable gates | Subject matter itself |

## 3. Layers

The spec has four layers. Each can be adopted separately.

1. **Principles (§4).** The claims about learning the rest depends on, each graded by strength of evidence.
2. **Content format (§5–6).** Files an author writes.
3. **Tutor protocol (§7–8).** What a tutor does with those files.
4. **Learner record (§9).** What a tutor emits after each lesson.

Domain packs (§11) extend the format for a subject. The base format is subject-neutral.

## 4. Principles

Every rule in this spec traces to one of these. Grades: **A**, replicated and safe to design on; **B**, credible but bounded; **C**, contested, used as a template rather than a law.

| # | Principle | Grade | Evidence | Consequence in the spec |
|---|---|---|---|---|
| P1 | A confident error that is then corrected is remembered better than a timid one (hypercorrection). | A | Butterfield & Metcalfe 2001 | Commit requires a confidence before the reveal. |
| P2 | A failed attempt to retrieve, followed by clear feedback, beats passive study. | A | Roediger & Karpicke 2006; Kornell, Hays & Bjork 2009; Richland et al. 2009 | Commit comes before any explanation. |
| P3 | Content the learner generates sticks better than content they are shown. | A (simple items), B (concepts) | Slamecka & Graf 1978 | Learners predict and explain; tutors don't do it for them. |
| P4 | Explaining *why* drives repair. | A | Chi et al. 1989, 1994 | Every lesson has an own-words exercise. Bare answers get no credit. |
| P5 | A broken prediction alone does not change a concept. The replacement must be intelligible, plausible, and fruitful. | A (as a critique) | Posner et al. 1982; Limón 2001 | Repair is mandatory and has three moves (§7.3). |
| P6 | Help should come at a real impasse and be minimal. | A | Koedinger & Aleven 2007; VanLehn et al. 2003 | At most three narrowing questions, then the answer (§7.3). |
| P7 | Scaffolding that helps a novice hinders someone who already knows the material (expertise reversal). | A | Kalyuga et al. 2003 | Confident and correct means skip the repair and go to the Twist. |
| P8 | Several representations of one idea build transfer, if the learner is helped to translate between them. | B | Ainsworth 2006 | Truth carries a "picture" paragraph; unify lessons exist. |
| P9 | Struggling before instruction improves transfer. | C | Kapur; Sinha & Kapur 2021 (d≈0.36); disputed by CLT researchers | Most lessons confirm; only about 1 in 4 traps. Sequencing stays tunable. |
| P10 | Curiosity peaks when the answer feels almost in reach, and boosts memory of what follows. | B | Loewenstein 1994; Kidd & Hayden 2015; Gruber et al. 2014 | Commits use concrete, small, nearly answerable cases. |
| P11 | Spacing and interleaving help retention at a cost to performance during practice. | A | Cepeda et al. 2006; Rohrer & Taylor 2007 | Reserved for review scheduling (open question §13). |
| P12 | Rewards contingent on completion undermine intrinsic interest. Guilt and hard resets drive people away. | A | Deci, Koestner & Ryan 1999; Lally et al. 2010 | No badges on the thinking; forgiving continuity; no guilt (§8). |
| P13 | Refusing to answer drives voluntary learners away even when it helps learning. | B | Khanmigo field RCT vs. mandatory-use RCTs | Never withhold indefinitely (§7.3). |

Not leaned on: Bloom's 2-sigma (use VanLehn 2011's ~0.76), Zeigarnik memory effects, generic growth-mindset messaging, exact flow ratios.

## 5. Content model

### 5.1 Glossary

- **Course.** An ordered set of flights on one subject, with one syllabus and one book.
- **Flight.** A chapter: 3–5 lessons built around one **target misconception**, finishable in one sitting.
- **Lesson.** One loop of commit, break, repair. The atomic unit of teaching and of recording.
- **Kind.** A lesson's role: `confirm`, `break`, or `unify` (§5.3).
- **Book.** The course's public explanatory text, one anchored section per lesson.
- **Target misconception.** The plausible wrong belief a flight exists to fix, stated in the syllabus.
- **Trap.** A Commit designed so that the plausible answer is wrong.
- **Audit.** A plausible but false statement the tutor makes on purpose to see whether the learner catches it.

### 5.2 Files

```
<course>/
  course.yaml      # manifest: metadata, flights, lessons
  syllabus.md      # public: what the course is, prerequisites, one paragraph per flight
  reading.md       # public: the book
  lessons/*.md     # served to tutors only, one per lesson
  authoring.md     # private: sources, licenses, decisions, status (never served)
```

**Visibility is part of the design.** The syllabus and book are public, and learners may read them first. Lessons are tutor-facing because they contain the Truth. Authoring notes stay private.

### 5.3 Manifest (`course.yaml`)

```yaml
openpedagogy: "0.1"
id: linear-algebra
title: Linear Algebra
summary: <one or two sentences>
status: draft | in progress | complete
prerequisites: <what is needed, phrased positively>
flights:
  - id: 01-arrows-and-columns
    title: Arrows and columns
    misconception: "Reaching every point needs perpendicular directions."   # proposed field
    lessons:
      - id: 03-reaching-every-point
        title: Reaching every point
        kind: break            # confirm | break | unify
        file: lessons/01-03-reaching-every-point.md
        reading: linear-combinations-and-reach   # anchor in reading.md
        builds_on: [01-two-numbers-one-arrow]    # proposed; required for unify
```

A flight with `lessons: []` is a planned flight. Tutors MUST skip it.

### 5.4 Lesson kinds and mix

| Kind | The Commit should | Share of a flight |
|---|---|---|
| `confirm` | succeed, and extend what the learner already has | most lessons |
| `break` | fail for most learners, for an identifiable reason | ~1 in 4; at least one per flight |
| `unify` | reveal that two or more earlier lessons are one idea | SHOULD close a flight that has a unify-able pair |

A flight SHOULD end on `unify` or on a lesson that sets up the next flight's misconception.

### 5.5 Lesson file grammar

A lesson is Markdown with a `#` title and these `##` sections, in this order:

| Section | Required | Shown to learner | Contract |
|---|---|---|---|
| **Commit** | MUST | yes, first | A prediction prompt with concrete values. MUST ask for a confidence from 1 to 4. MAY have several parts that escalate. |
| **Truth** | MUST | after commit | The answer to every part, worked. Then a paragraph that gives the picture: why it holds, in a second representation. |
| **Repair** | MUST | paraphrased | Why the idea held *in this case*, using the Commit's numbers. ≤150 words. Never a general explanation; that belongs in the book. |
| **Artifact** | MAY | if tutor can build | A spec for an interactive that follows the predict-then-reveal pattern (§7.5). |
| **Exercises** | MUST | after repair | At least one item that only the learner can answer (§6.4). |
| **Twist** | MUST | to confident and correct learners | A harder variant that pushes past the lesson, often toward the next lesson or flight. |

No other sections are allowed. A "Concept" or "Explanation" section is a conformance failure, because explanations live once, in the book.

### 5.6 Book (`reading.md`)

- One `###` section per lesson, anchored `{#id}`, in course order, grouped by `##` flight.
- Every lesson's `reading` MUST resolve to a section, and every section MUST be used by some lesson.
- ≤450 words per section.
- Each section covers three things: **the idea** (stated plainly, term defined on first use), **why it holds**, and **where else it applies**, both outside the subject and later in the course.
- The book MUST NOT contain any lesson's Commit values or Truth. It explains with its own examples, so reading it first prepares the learner without spoiling the lesson.
- Sources and further reading go at the end, with their license.

### 5.7 Syllabus

- Prerequisites stated as abilities ("solve 2x + 3 = 7"), not as course names, and never as a list of what is *not* needed.
- One paragraph per flight that names its target misconception in plain words ("Three arrows in space do not always reach all of space").
- States the single thing the course demands of the learner: commit before seeing the answer.

## 6. Authoring practices

These rules come from writing and reviewing the first flight of Linear Algebra. Each cites the lesson it comes from.

### 6.1 Designing a trap

- Build the trap around a **feature that looks relevant and isn't**. In 1.3 both arrows lean the same way and are not perpendicular. Learners fixate on perpendicularity, but the real condition is "not on one line."
- Pick the target to maximize the pull of the wrong answer: (0, 5) sits where neither arrow points.
- Follow every `break` with a `confirm` that shows the real condition failing (1.4: u = 2v), so the learner sees both sides of the boundary.
- The Repair MUST name what never mattered and what would have: "Leaning the same way never mattered, and neither did being perpendicular. Lying on one line would have."

### 6.2 Designing a Commit

- Use concrete, small numbers that can be checked by hand. No symbols the learner hasn't met.
- Use parts that escalate: a warm-up the learner will get right, then the real question (1.1: write the column, then "is it the same vector?").
- `confirm` lessons SHOULD still hide a small surprise in their last part (1.1: displacement versus location; 1.2: does order matter?).
- Ask for a prediction, not a computation, whenever the concept is about structure ("can you reach *every* point?").

### 6.3 Writing the Truth

- Answer every part with the arithmetic shown, then a check line.
- Add one paragraph that switches representation (algebraic to picture, or the reverse). This is the multiple-representation duty (P8).
- A `unify` Truth re-reads earlier lessons in the new language, by number ("Lesson 4 in this language: …").

### 6.4 Exercises: what counts as "only the learner can answer"

The learner has an LLM, so any question that can be pasted into one will be. "Human-only" can't be enforced, only designed toward. Each lesson SHOULD include at least one of:

1. **Own words.** "In your own words, explain…". The tutor follows up live on the learner's specific wording.
2. **Own choice.** "Choose any w so that…", which yields an artifact unique to the learner (1.4 ex. 2).
3. **Own history.** "What did this chapter break for you? If nothing broke, what would you have bet on?" (1.5 ex. 3).
4. **Own sandbox state.** A question about what the learner saw in an artifact they manipulated.

A typical lesson has three exercises in a fixed progression: compute, then invert or describe without numbers, then own words.

### 6.5 Twists

- A Twist SHOULD look forward. It previews the next lesson's idea (1.3 Twist, a redundant third arrow, leads into 1.4) or the next flight's misconception (1.4 Twist, two arrows in 3D, leads into flight 2).
- A Twist MUST be solvable with what the lesson taught plus one new step.

### 6.6 Sources and licensing

- Record every source in `authoring.md` with its license, and verify the license from the source itself before adapting anything.
- Don't redistribute sources whose license forbids it. Credit adapted arcs in the book's footer.

### 6.7 Prose

- Plain words. Define a term the first time it is needed and not before. Prefer the intuitive term and give the textbook term in parentheses once ("reach (textbooks call it span)").
- No praise, no hype, no "simply" or "obviously".

## 7. Tutor protocol

### 7.1 Inputs

For each lesson the tutor receives: `id`, `kind`, the lesson text, and its `reading` section. **The reading is the tutor's authority on the concept.** Explanations MUST be grounded in it. The tutor MAY go beyond it only when the learner asks.

### 7.2 The loop

```
Commit ──► Break ──┬─ confident & wrong ──► Repair (full Socratic) ──► Exercise ──► Close
                   ├─ confident & right ──► Twist ───────────────────► Exercise ──► Close
                   ├─ unsure & right ─────► Repair (brief) ──────────► Exercise ──► Close
                   └─ unsure & wrong ─────► Scaffold / step back ────► Repair ─────► …
```

"Confident" means 3–4; "unsure" means 1–2.

1. **Commit.** Present only the Commit. The tutor MUST NOT continue until it has both a prediction and a confidence.
2. **Break.** Reveal the Truth, then branch:
   - *Confident and wrong.* The best moment in the course (P1). Full repair.
   - *Confident and right.* Skip the repair and give the Twist (P7).
   - *Unsure and right.* Say it was right, then repair anyway, because a guess is not knowledge.
   - *Unsure and wrong.* A prerequisite is probably missing. Scaffold down by removing a dimension or using a simpler case. If the learner can't name what they reasoned from, return to the earlier lesson.
3. **Repair (§7.3).**
4. **Exercise.** Give the Exercises. Refuse plug-and-chug: a correct answer without reasoning gets a question, not credit. If the reasoning is a procedure with no explanation, the tutor says so and asks again.
5. **Close.** Emit the attempt record (§9). Fetch the next lesson. Continue only if the learner has energy; otherwise say in one line where they stopped.

### 7.3 Repair

- Ask what the learner expected and why.
- Ask **at most three** questions that narrow toward the broken assumption. If the learner hasn't moved after three, give the repair outright and mark the attempt `assisted: true`. The tutor MUST NOT withhold indefinitely (P6, P13).
- Repair in **three moves** (P5):
  1. *Intelligible.* State the idea plainly, from the reading.
  2. *Plausible.* Show why it held in this case, from the lesson's Repair.
  3. *Fruitful.* Name one other place it applies, from the reading.
- Paraphrase. MUST NOT paste sections.

### 7.4 Conduct rules

| Rule | Limit |
|---|---|
| Words before handing the turn back | ≤150, ending in a question or a task |
| Sections shown at once | only the one the loop is at; never the whole lesson |
| Answering its own question, or simulating the learner's reply | never |
| Audits | once per flight: one plausible false statement, revealed afterward either way |
| Notes about the learner kept outside the record (§9) | never |
| Praise inflation, guilt, moralizing about gaps | never; report outcomes plainly |
| Failure feedback | attribute to strategy or approach, never to ability |

### 7.5 Interactive artifacts (optional)

If a tutor can produce interactives, they follow one pattern: **predict, then reveal.**

- The reveal MUST be locked until the learner commits a prediction and a confidence.
- The animation MUST show *why* the truth holds, not just *what* it is.
- The artifact ends with a single result line the learner pastes back (prediction, confidence, outcome). That line goes into `artifact_result`.
- Reference profile: a single HTML file, SVG and plain JavaScript, no dependencies, no network access.

Artifacts matter most where the claim is about a picture. Authors SHOULD write Artifact sections for those lessons first.

### 7.6 Sessions

- Start from the learner record. If a next lesson exists, go straight to its Commit with no recap.
- The first lesson ever: ask in two sentences what the learner remembers from earlier exposure. No placement test. Ask them to say one if-then plan aloud ("when I have twenty minutes and energy, I open this"). Don't store the plan.
- Continuity (a streak) is weekly and forgiving. Mention it in a few words, if at all.

## 8. Engagement constraints

These are normative because getting them wrong destroys the learning loop (P12, P13).

- MUST NOT attach points, badges, or loot to solving. The content is the reward.
- MUST NOT punish or shame a gap. Re-entry resumes exactly where the learner left off.
- Continuity mechanics, if present, MUST forgive misses (a weekly window, never a daily hard reset).
- SHOULD give the learner real choice of course and pace.
- SHOULD track learning separately from engagement. They come apart.

## 9. Learner record

Each completed lesson emits one **attempt**:

```json
{
  "openpedagogy": "0.1",
  "course": "linear-algebra",
  "lesson": "03-reaching-every-point",
  "commit": "No for (0,5); no for every point, they aren't perpendicular",
  "confidence": 4,
  "outcome": "wrong",
  "assisted": false,
  "artifact_result": null,
  "at": "2026-11-04T18:22:00Z"
}
```

| Field | Type | Meaning |
|---|---|---|
| `commit` | string | The learner's prediction, verbatim or a close paraphrase |
| `confidence` | int 1–4 | Self-reported before the reveal |
| `outcome` | `correct` \| `wrong` | Of the Commit, not of the exercises |
| `assisted` | bool | The tutor gave the repair outright after three questions |
| `artifact_result` | string? | The result line pasted back from an artifact |

The pair (confidence, outcome) is the core signal. A wrong answer at confidence 4 is the most valuable event the system records. Proposed fields for v0.2: `branch`, `exercise_quality`, `audit_caught`.

## 10. Binding: tutor access (informative)

One reference binding, as implemented by openacademy's server and the Plato plugin over MCP:

| Tool | Returns / does |
|---|---|
| `list_courses()` | catalog |
| `start_course(field, subfield, course)` | enrollment and first lesson |
| `next_lesson(enrollment_id)` | `{id, kind, text, reading}` |
| `record_attempt(...)` | stores a §9 attempt |
| `progress()` | enrollments, next lessons, continuity |

**Proposed:** the protocol (§7–8) is published as a versioned, machine-readable document (`openpedagogy/protocol/0.1.md`) that a server returns in its MCP `instructions`. Plato then carries no pedagogy of its own. It loads the protocol version the course declares in `openpedagogy:`.

## 11. Domain packs

The base format is subject-neutral. A domain pack adds:

- **Lenses.** The representations the subject uses, e.g. algebraic, geometric, and computational for linear algebra.
- **Misconception catalog.** Known wrong beliefs with citations, e.g. Dorier and Sierpinska on span, basis, and independence.
- **Artifact library.** Reusable interactive templates.
- **Audit library.** Plausible but false statements, e.g. "a proof that inverts a non-square matrix."

A pack MUST NOT change the loop or the lesson grammar. If a subject seems to need that, the base spec has a gap.

## 12. Conformance

### 12.1 Levels

| Level | Content | Tutor |
|---|---|---|
| **Core** | Passes all gates in §12.2 | Runs §7.2–7.4 and emits §9 |
| **Full** | Core, plus misconception per flight, `builds_on` on every unify, at least one Artifact per flight | Core, plus audits, artifacts, and the §7.6 session rules |

### 12.2 Machine-checkable gates

Running today in openacademy as `src/tests/test_content.py`:

1. Lesson sections ⊆ {Commit, Truth, Repair, Artifact, Exercises, Twist}; the required ones appear in order.
2. Commit mentions a confidence.
3. Repair ≤150 words.
4. Exercises contain an own-words item.
5. Every lesson's `reading` resolves; every book section is used; each ≤450 words.

Proposed:

6. Every non-empty flight has at least one `break`; breaks make up ≤40% of its lessons.
7. Each `unify` lesson has `builds_on` pointing to earlier lessons.
8. No Commit number tuple appears in `reading.md`, as a spoiler check.
9. Flight size is 3–5 lessons.

### 12.3 Not machine-checkable (review checklist)

- Does the trap pull toward a specific, nameable wrong belief?
- Does the Repair use this case's numbers rather than restating the book?
- Would an LLM fully answer the own-words item without the learner's context? If yes, rewrite it.
- Does the Truth's picture paragraph add a representation, not repeat the first?

## 13. Open questions

Roughly in order of how much they block.

1. **Advancement.** The loop never fails a learner, and outcome doesn't gate the next lesson. Should a wrong/assisted streak trigger stepping back or review? (Math Academy uses mastery gating.)
2. **Review and spacing.** P11 is grade A, but the spec has no review mechanism. Options: retired Commits resurface as quick re-commits, or spaced Twists.
3. **Sequencing per learner.** P9 is contested. Should a learner who is confident and correct on several lessons get explain-first lessons?
4. **Exercise grading.** "Structural reasoning, not keywords" needs a rubric a tutor can apply consistently.
5. **Dosage budget.** "About 1 in 4" and "one audit per flight" are fixed ratios. Should they adapt to observed patience (session length, energy)?
6. **Generated vs. curated content.** May a tutor generate a lesson on the fly from a book section, and if so, at what conformance level?
7. **Non-mathematical domains.** Does commit-break-repair hold for history, language, or craft? Writing a second domain pack is the test.
8. **Versioning.** How a course pins a protocol version, and how tutors handle a course that declares a newer one.
9. **Naming.** "Flight" vs. "chapter", "kind" vs. "role". Settle before 1.0.

## Appendix A. Annotated lesson (1.3, `break`)

| Section | What it does | Rule |
|---|---|---|
| Commit: v=(2,1), w=(1,1), "they lean toward the same side… reach (0,5)? reach every point?" | Draws attention to a feature that looks relevant and isn't; the target sits where neither arrow points | §6.1, §6.2 |
| Truth: a=−5, b=10, then the general solution, then "a skewed grid still covers the whole plane" | Worked answer with a check, then the picture | §6.3, P8 |
| Repair: "Leaning the same way never mattered… Lying on one line would have." | Uses the case's numbers; names what never mattered and what would have | §6.1, §5.5 |
| Artifact: lock a yes/no guess, then sliders, then the skewed grid | Predict, then reveal | §7.5 |
| Exercises: compute (7,2); hit (1,0) and (0,1); "in your own words, state the real condition" | Compute, then generalize, then own words | §6.4 |
| Twist: a redundant third arrow u=(3,0) and two different triples | Leads into lesson 1.4 (redundancy) | §6.5 |

## Appendix B. Evidence

Full notes with boundary conditions live in openacademy's `docs/research/learning-science.md`, `engagement-and-habit.md`, and `products-and-methods.md`. Before 1.0 they should move into the spec repo as `evidence/`, one file per principle, each with its grade and the date it was last reviewed.

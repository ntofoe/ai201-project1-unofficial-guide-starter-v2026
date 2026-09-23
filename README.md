# The Unofficial Guide

**Nicholas Tetteh Ofoe | Corpus: `campus_life`**

> **This file is your submission.** Fill it in as you go — most sections get
> written during the milestone that produces them, not at the end.
>
> How the starter works, and every command you'll need, is in `RUNNING.md`.
> Leave that file alone.
>
> **Paste everything as text.** No screenshots, no video. A typed table gets
> full credit; a picture of the same table gets none.
>
> Delete these instruction blocks as you replace them. The `<!-- -->` comments
> are notes to you and don't show up when the page renders — you can leave them
> or remove them.

---

# Unit 1

## What This Does

This project answers factual questions using 88 short posts from the
`campus_life` corpus. The documents cover administrative policies, courses,
dining, housing, and other parts of student life. For each question, the system
retrieves the closest posts and generates an answer using only those sources.
It cites the source files and refuses questions whose best distance exceeds
the 0.60 relevance cutoff.

## Chunking Strategy

**Chunk size:** 600 characters (maximum)
**Overlap:** none

I chose a maximum chunk size of 600 characters with no overlap. All five
sampled chunks stood on their own, preserved their headings, and contained
complete sentences. The corpus consists of short, single-topic documents
averaging 317 characters, with the longest containing 549 characters. Keeping
each document intact avoids separating details from the course, residence, or
dining-hall name that gives them context. Documents longer than 600 characters
are divided only at natural paragraph or sentence boundaries.

## Sample Chunks

<!-- Five chunks, pasted as text. Label each one and name the file it came from
     AND the function that produced it — the grader checks your code against
     what you claim here.

     `python app.py chunks -n 5` prints all three for you. Copy them straight
     across.

     Milestone 3. -->

**Chunk 1** — source: `admin_add_drop_deadline.txt#0` — produced by: `chunker.py::split_documents`

```
On the add/drop deadline

You can add a course through the end of the second week. Dropping is a longer window — through the end of week six — but a drop after week two shows as a W on your transcript. Nothing anywhere on the registrar's site says this plainly, and students find out from each other.
```

**Chunk 2** — source: `course_biol_160.txt#0` — produced by: `chunker.py::split_documents`

```
BIOL 160 Cell Biology

I lived here my sophomore year. Format is lecture three times a week with a weekly lab. Assessment: four unit tests and a cumulative final. Not curved.

Expect 9 to 11 hours a week, the heaviest first-year course by reputation.

The one piece of advice: the unit tests come fast, roughly every three weeks; falling behind once is very hard to recover from.
```

**Chunk 3** — source: `course_hist_118_workload.txt#0` — produced by: `chunker.py::split_documents`

```
Workload for HIST 118 Modern World History

People keep asking so: a lot of reading, about 120 pages a week, but no problem sets. That's real time, not optimistic time.

It's front-loaded — the first month is heavier than the rest, partly because you're learning the format.
```

**Chunk 4** — source: `dining_pellew_dining_hall_followup.txt#0` — produced by: `chunker.py::split_documents`

```
Re: Pellew Dining Hall

Adding to what people have said about Pellew Dining Hall. The wait figure of 12 to 18 minutes at peak matches what I've seen. If you're trying to eat between classes, go before 11:45 and it's a different building entirely.

Also worth saying: the furthest hall from anywhere, next to the athletics centre. Nobody tells you this at orientation.
```

**Chunk 5** — source: `housing_innisfree_hall.txt#0` — produced by: `chunker.py::split_documents`

```
Innisfree Hall — what it's actually like

Transferred in last year, so take this with a grain of salt. Built 1991, renovated 2022. Rooms are doubles arranged as pairs sharing one bathroom between two rooms.

The good: the shared-bathroom-between-two-rooms arrangement is the best compromise on campus.

The bad: no air conditioning, which matters for the first three weeks of September.

Laundry costs $1.75 wash, $1.75 dry, app-based. On noise: moderate; the building is L-shaped and the short wing is much quieter.
```
## Sample Answer


**Question:** How many midterms are given in CS 210?

**Answer:**

```
(best distance 0.304, cutoff 0.6)

There are two midterms given in CS 210.

Source: course_cs_210.txt (and course_cs_210_exams.txt)

Sources retrieved: course_cs_210.txt, course_cs_210_exams.txt, course_cs_340_exams.txt, course_math_220_exams.txt, course_stat_150_exams.txt
```

**My relevance cutoff:** `0.60`

The five in-corpus questions had best distances between 0.1856 and 0.3773.
The five out-of-scope questions had best distances between 0.8246 and 0.9340.
This left a gap from 0.3773 to 0.8246. I kept the cutoff at 0.60 because it is
near the middle of that gap. With this cutoff, all five in-corpus questions
passed the gate and all five out-of-scope questions were refused.

| Question | In corpus? | Best distance |
|---|---:|---:|
| How much printing credit does each student receive per semester? | Yes | 0.3719 |
| What is the last week in which a student can add a course? | Yes | 0.3773 |
| How many midterms are given in CS 210? | Yes | 0.3042 |
| How quickly do student parking permits for the west lots usually sell out? | Yes | 0.1856 |
| When are housing-lottery numbers released? | Yes | 0.3772 |
| What is the capital of Mongolia? | No | 0.8246 |
| How do I change the oil in a diesel engine? | No | 0.9340 |
| Who won the 1994 World Cup? | No | 0.8859 |
| What is the recommended dosage of ibuprofen for a headache? | No | 0.8442 |
| How do I write a for loop in Rust? | No | 0.8960 |

## How I Used AI

**1.** I described my chunking rule in plain English (600-character max, no overlap, split only at paragraph or sentence boundaries) and asked Claude to write the Python function. It came back working correctly on the first try against my campus_life corpus, so I didn't need to change the logic — I tested it with `python app.py index` and `python app.py chunks -n 5` to confirm it matched what I'd described.

**2.** When indexing failed with a cryptic ONNXRuntimeError from CoreML, I pasted the full error to Claude. It correctly diagnosed it as an Apple Neural Engine incompatibility. The first fix it suggested (downgrading onnxruntime) didn't work, so it searched further and found the actual fix — forcing `preferred_providers=["CPUExecutionProvider"]` in `store.py` — which I applied and confirmed worked.

<!-- ── Stretch features ─────────────────────────────────────────────────────
     Doing one? Say so here BEFORE you start. A feature this README never
     claims earns nothing.
     ───────────────────────────────────────────────────────────────────────── -->

---

# Unit 2

## Run Log — Before

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Sampled chunks preserve complete information | 8 of 10 | 10/10 | 10/10 | 10/10 | MET |
| 5. Source citations support the answers | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |

Criterion 3 is measured in one deterministic pass since the gate is a fixed comparison, so
the same 5/5 appears in all three run columns. Criterion 4 was also measured once (10
chunks sampled) for the same reason — chunking is deterministic and doesn't vary between
runs.

**Sample output** — produced by `run_eval.py::main`, from `results/run_2026-09-23_1920_before.md`:

**Question:** How much printing credit does each student receive per semester?

Each student receives $30 of printing per semester. (Source: admin_printing_quota.txt)

**Question:** How many midterms are given in CS 210?

Two midterms are given in CS 210, as stated in the documents course_cs_210_exams.txt and course_cs_210.txt.


## Verdicts

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 | Retrieved chunk contains the answer | MET | All 5 questions had their correct source file in the retrieved chunks, across all 3 runs (5/5 every time), comfortably above the 4/5 target. |
| 2 | Every answer names a source | MET | Every one of the 15 answers (5 questions × 3 runs) explicitly named its source file. |
| 3 | Gate stops out-of-corpus questions | MET | All 5 out-of-scope questions were refused by the gate, matching the deterministic single-pass measurement. |
| 4 | Sampled chunks preserve complete information | MET | All 10 sampled chunks ended at a clean sentence boundary and were understandable without needing the surrounding text. |
| 5 | Source citations support the answers | MET | For every question, the cited source file was the same one that actually contained the fact used in the answer — no answer cited an unrelated file. |

## Diagnoses

I missed nothing on this run — all five criteria were MET. Per the note above about setting
safe targets, I looked honestly at where I had the most slack.

Criterion 1 (retrieved chunk contains the answer) and criterion 3 (gate stops out-of-corpus
questions) both came out 5/5 against a 4/5 target. Criterion 3 is a deterministic pass/fail
comparison against a fixed distance cutoff, so there isn't a meaningful way to "get lucky" on
it — it's simple rather than loose.

Criterion 1 is the one I'd tighten. As written, it only checks whether the correct chunk appears
*anywhere* in the top 5 retrieved results. A chunk could rank 5th out of 5 and still count as a
pass, even though a system using a smaller top-k (2 or 3, which is common for keeping prompts
short) would never actually see it. That's a real gap between what the criterion measures and
what the system needs to do well.

**Revised in unit 2:** For at least 4 of my 5 test questions, the answer appears within the
top 2 retrieved chunks (not just anywhere in the top 5).

**Why revised:** The original criterion couldn't distinguish a system that ranks answers first
from one that barely surfaces them at rank 5. The top-2 version tests something a smaller,
more realistic top-k would actually need to satisfy.

## The Improvement

**What I changed:**

**Why I picked it:**

<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 |  |  |  |  |
| 2. Every answer names a source | 5 of 5 |  |  |  |  |
| 3. Gate stops out-of-corpus questions | 4 of 5 |  |  |  |  |
| 4. | | | | | |
| 5. | | | | | |

**Did it help?**

<!-- Say plainly whether it did, and how you know. If it made things worse,
     say that — a change that backfired, honestly reported, earns full credit
     and is more interesting than one that worked. What matters is that you can
     tell.

     Milestone 4. -->

## What's Still Broken

<!-- For each criterion still missed after your fix: what you'd do about it,
     and why you stopped where you did.

     "I ran out of time" is fine if it's true. Pretending nothing is left is
     not.

     Milestone 5. -->

## What I'd Do Differently

<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->

# The Unofficial Guide

Andrew Ansah - `campus_life` corpus

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

This project is an AI-powered retrieval-augmented generation (RAG) system built to answer questions about college life. It operates on the `campus_life` corpus, which is a collection of short, densely packed student reviews and guides covering topics like dining halls, course workloads, and housing lotteries. The system retrieves precise excerpts from these documents and uses them to give highly accurate, concise, and fully cited answers, effectively serving as an unofficial, automated student handbook.

## Chunking Strategy

**Chunk size:** Split by natural paragraphs (`\n\n`)
**Overlap:** 0 characters

For the `campus_life` corpus, each document is a short post with 1-3 paragraphs, and useful information often sits in a single sentence. The original 800-character limit rarely split anything, meaning entire multi-thought posts were lumped into single chunks. By splitting on natural paragraph boundaries (`\n\n`), we ensure that each complete thought (e.g., a specific point about exams or housing lottery) stays intact as its own distinct chunk, preventing unrelated thoughts in the same post from diluting search relevance without ever slicing a critical sentence in half.


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
```

**Chunk 2** — source: `course_cs_210_workload.txt#2` — produced by: `chunker.py::split_documents`

```
It's front-loaded — the first month is heavier than the rest, partly because you're learning the format.
```

**Chunk 3** — source: `course_phys_130.txt#3` — produced by: `chunker.py::split_documents`

```
The one piece of advice: the lab practical is worth 20% and almost nobody prepares for it.
```

**Chunk 4** — source: `dining_verrill_street_grill.txt#1` — produced by: `chunker.py::split_documents`

```
I'm a junior and I've done this twice now. Wait times: up to 30 minutes on Friday evenings, otherwise under 10. The thing worth going for is the burger, which is the only late-night hot food on campus. The thing to know is that one register, so the queue is a single line no matter how busy.
```

**Chunk 5** — source: `housing_morrow_house.txt#2` — produced by: `chunker.py::split_documents`

```
The good: cheapest housing tier by about $900 a year, and the singles are real singles.
```

## Sample Answer

<!-- One complete question and answer, pasted as text, with the source line
     visible. Milestone 4. -->

**Question:** How are juniors and seniors ordered in the housing lottery?

**Answer:**

```
Juniors and seniors are ordered by accumulated credit hours first, with ties broken randomly (admin_housing_lottery.txt).

Sources retrieved: admin_housing_lottery.txt, housing_aldridge_hall.txt, housing_innisfree_hall.txt, housing_morrow_house.txt
```

**My relevance cutoff:** 0.55

When measuring the distances, the in-corpus questions had best distances ranging from 0.23 to 0.39. The out-of-scope questions had best distances ranging from 0.78 to 0.85. Because there was a massive, clean gap between 0.39 and 0.78, setting the cutoff securely in the middle at 0.55 guarantees that valid questions are easily accepted while irrelevant ones are aggressively gated out.

| Question | In corpus? | Best distance |
|---|---|---|
| How are juniors and seniors ordered in the housing lottery? | Yes | 0.2343 |
| Are the midterms and final for CS 210 curved? | Yes | 0.3294 |
| How often does the regional menu change at North Kitchen? | Yes | 0.3931 |
| Which floors in Aldridge Hall have strictly enforced quiet hours? | Yes | 0.2779 |
| What is the cost of doing a load of laundry (wash and dry) in Aldridge Hall? | Yes | 0.2753 |
| What is the capital of Mongolia? | No | 0.7986 |
| How do I change the oil in a diesel engine? | No | 0.8502 |
| Who won the 1994 World Cup? | No | 0.7803 |
| What is the recommended dosage of ibuprofen for a headache? | No | 0.8243 |
| How do I write a for loop in Rust? | No | 0.8313 |

## How I Used AI

**1.** I asked the AI coding assistant to write my chunking function based on my decision to split strictly by paragraphs (`\n\n`). The AI generated the loop and the `Chunk` object instantiations perfectly, but I ensured that it explicitly stripped out whitespace and ignored empty paragraphs so we wouldn't index blank chunks.

**2.** I asked the AI to help me analyze the output of the distance testing script when hunting for the relevance gap. It accurately pointed out that the gap was between 0.39 and 0.78, and proposed 0.55 as a safe midpoint. I reviewed the numbers and confirmed this was the optimal threshold to implement in `config.py` to prevent hallucination without falsely refusing valid queries.

<!-- ── Stretch features ─────────────────────────────────────────────────────
     Doing one? Say so here BEFORE you start. A feature this README never
     claims earns nothing.
     ───────────────────────────────────────────────────────────────────────── -->

---

# Unit 2

<!-- These sections get ADDED to what's already above. Don't delete or rewrite
     unit 1 — the point is that someone can see what you said before you knew
     how it went. -->

## Run Log — Before

<!-- Your five criteria, three runs each. `python run_eval.py --label before`
     runs the questions, puts the OUT_OF_SCOPE ones through the gate, and
     writes it all into results/ for you. Targets come from criteria.md; the
     verdict column is your call.

     Criterion 3 is measured in one deterministic pass rather than three, so
     the same number goes in all three run columns. That's correct, not lazy.

     Milestone 1. -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 4/5 | 4/5 | 4/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Chunks capture a complete thought | 4 of 5 | 4/5 | 4/5 | 4/5 | MET |
| 5. Model answers concisely (<= 3 sentences) | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |

**Real output for each criterion (from Run 1):**

*Produced by `generate.py::answer_from_chunks`*
**(Criteria 1, 2, 4, 5)** 
Question: *How often does the regional menu change at North Kitchen?*
Output:
```
The regional menu at North Kitchen changes fortnightly (dining_north_kitchen.txt).
```

*Produced by `gate.py::check`*
**(Criterion 3)**
Question: *What is the capital of Mongolia?*
Output: `refused  (best distance 0.799)  What is the capital of Mongolia?`

<!-- Underneath, paste the REAL output for each criterion from one of your
     runs — the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->

## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 | Retrieved chunk contains the answer | MET | Q1-Q4 successfully retrieved the chunk with the answer. Q5 failed, but 4/5 meets the target. |
| 2 | Every answer names a source | MET | The model appended the correct source filename to the end of every answer across all runs. |
| 3 | Gate stops out-of-corpus questions | MET | The gate firmly refused 5 out of 5 out-of-scope questions (target was at least 4 of 5). |
| 4 | Chunks capture a complete thought | MET | By chunking by paragraph, every chunk was naturally a complete thought. |
| 5 | Model answers concisely | MET | Thanks to the tightened grounding instruction, no answer exceeded a single sentence. |

## Diagnoses

Even though all criteria were technically "MET", Criterion 1 was only met because the target was set low (4 of 5). Q5 ("What is the cost of doing a load of laundry (wash and dry) in Aldridge Hall?") consistently failed to answer because it couldn't retrieve the right chunk.

**Diagnosis for Q5 failure (Chunking Stage):** 
The issue happened in the chunking stage. Because we split the document strictly by paragraphs (`\n\n`), the first chunk contained the building name ("Laundry in Aldridge Hall"), but the second chunk only contained the prices ("Machines take $1.75 wash..."). The query asked about "Aldridge Hall", so the retrieval stage grabbed the first chunk but missed the second chunk completely. The generation stage correctly stated it didn't have the info, but the root cause was the chunking strategy stripping context from the paragraph containing the answer.

     The five stages: loading → chunking → embedding → retrieval → generation.

     Look for a pattern. If three misses all ask about numbers, that's one
     problem, not three.

     Missed nothing? Say so, then say honestly whether your targets were set
     low, and which one you'd tighten and to what.

     Milestone 3. -->

## The Improvement

**What I changed:**
I updated `chunker.py` to preserve context. Instead of just splitting strictly by paragraphs (which stripped the title/building name from the subsequent text), the chunker now prepends the first paragraph (the title/context) to all other paragraphs in that document before creating the chunk.

**Why I picked it:**
This directly fixes the chunking diagnosis above by ensuring the chunk containing the laundry prices also contains the building name ("Laundry in Aldridge Hall"), making it highly retrievable for queries specifically asking about Aldridge Hall.

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Chunks capture a complete thought | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Model answers concisely (<= 3 sentences) | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |

**Did it help?**
Yes, it helped immensely. In the "before" run, Question 5 failed entirely across all three runs because the best distance for the correct document was a weak `0.2753`, completely missing the right chunk. In the "after" run, by simply prepending the context, the distance dropped significantly to `0.2138` (a much stronger match). All three runs for Q5 successfully retrieved the prices and stated "$1.75 wash, $1.50 dry," meaning Criterion 1 is now effectively a 5/5 perfect score!

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

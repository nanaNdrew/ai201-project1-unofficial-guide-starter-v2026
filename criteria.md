# Acceptance criteria — The Unofficial Guide

Five criteria that say what "working" means for this system, written in unit 1
**before** any results existed.

An acceptance criterion names a target: a number, a count, a rate, or something
a person could plainly observe. *"Retrieval works"* is an opinion. *"For at
least 4 of my 5 test questions, the top results include a chunk containing the
answer"* is a criterion.

Under each one, write a sentence or two on **why that target** and not a
stricter or looser one. A reason that says something about your corpus or your
pipeline earns credit; *"80% seemed reasonable"* does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## 1. Retrieved chunks contain the answer

For at least 4 of my 5 test questions, the retrieved chunks include one that
contains the answer.

**Why this target:**
Since the corpus documents are very short, the embeddings might sometimes match nearby context over the precise sentence. 4 of 5 allows for one mismatch while ensuring the retrieval generally hones in on the right document.

---

## 2. Every answer names a source

Every answer the system produces names at least one source document.

**Why this target:**
Our system passes the filename as metadata to every chunk, and the prompt explicitly instructs the model to cite the source. A failure to cite indicates a prompt engineering failure or a refusal to answer, so a 100% target is expected.

---

## 3. The relevance gate stops out-of-corpus questions

When I ask a question my documents clearly don't cover, the relevance gate
stops it and the system returns "I don't have enough information about that" —
in at least 4 of 5 tries.

**Why this target:**
When testing out-of-scope questions, the distance metric typically has a relatively clean gap, but some generic questions might occasionally trick the gate due to semantic overlap. A 4 of 5 target ensures the gate is highly effective without demanding impossible perfection.

---

## 4. Chunks capture a complete thought

At least 4 of 5 randomly sampled chunks read as a complete, independent thought, with no sentences cut in half at either end.

**Why this target:**
The documents in the `campus_life` corpus are brief and densely packed with information. If chunks are too small or arbitrarily split, critical context is lost. A 4 of 5 target ensures the chunking strategy respects sentence boundaries the vast majority of the time.
---

## 5. Model answers concisely

For all 5 test questions, the model's generated answer must be 3 sentences or fewer.

**Why this target:**
Students reading the guide want quick, direct answers, just like the source documents themselves which are very punchy. 5 of 5 is achievable because we can strictly enforce brevity in the system prompt.
---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 2 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath it, like this:

         ## 1. Retrieved chunks contain the answer

         For at least 4 of my 5 test questions, the retrieved chunks include
         one that contains the answer.

         **Why this target:** ...

         > **Revised in unit 2:** For at least 4 of 5 questions, the top three
         > results contain the answer.
         >
         > **Why revised:** I couldn't judge "the chunks include one that
         > contains the answer" the same way twice — I scored two questions
         > differently on Monday than on Wednesday. The new version is
         > something I can actually check.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "I said 4 of 5 but got 2 of 5, so 2 of 5 is more realistic."

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.

     The whole reason the originals stay visible is so someone can see what you
     said before you knew the answer.
     ───────────────────────────────────────────────────────────────────────── -->

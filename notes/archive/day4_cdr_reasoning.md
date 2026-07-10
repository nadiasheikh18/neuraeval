# Day 4 — The CDR Question (reasoning capture)
*July 10, 2026. Component of the Day 4 reflection, not the whole thing — the MMSE-vs-age*
*scatter and the per-feature distributions are still to be done. This file records only the*
*CDR keep/drop reasoning, saved so it can be reviewed later rather than lost in chat.*

## Status of this decision
**Resolved in principle, not yet settled by test.** The one thing that turns "in principle"
into "decided" is reading the generator (see settling test below). I do not have the notebook
in front of me here, so nothing in this file is a verdict — it is the argument, held for review.

---

## The question
Is CDR a legitimate feature, or the label wearing a disguise?

## The trap
"CDR separates the stages almost perfectly" *feels* like it answers the question. It doesn't.
Near-perfect separation is a symptom that points **both** ways:
- A genuinely excellent independent measurement separates near-perfectly.
- A feature that is downstream of the label also separates near-perfectly.

So separation cannot settle it. **Provenance** can. The real question is not *how well does
CDR predict stage* — it is *where does CDR sit relative to how stage was assigned.*

---

## The settling test (synthetic data)
Read the generator. Ask how `cdr` is drawn.

- If `cdr = f(stage) + small_noise` — i.e. `stage` is picked first and CDR is derived from it —
  then CDR is not correlated *with* the label, it **is** the label with jitter on top. Dispositive.
- This is the same mechanism flagged in the Day 3 caveat: MMSE and age are "correlated *through*
  the label" because both are generated from `stage`. CDR would be the most extreme case of that —
  except where age overlaps messily, CDR was tuned to separate cleanly, which is exactly why it
  looks perfect.

This test is a single look at the source, not a statistic. The notebook was read cell-by-cell on
Day 3, so the answer may already be known — it just needs to be written down here once confirmed.

## The same test in real data (ADNI / PPMI)
No generator to read, so ask the provenance question directly:
**Does the diagnostic protocol use CDR to assign stage?**

CDR (Clinical Dementia Rating) is a *global staging instrument* — memory, orientation, judgment,
home/hobbies, personal care, scored by interview. Clinicians often stage *using* it. If the label
was produced by a process that consulted CDR, then a model learning CDR → stage is reconstructing
the clinician's arithmetic, not learning disease biology. It will look brilliant and transfer nothing.

---

## Why this matters more for NeuraEval specifically
NeuraEval's whole point is an *honestly uncertain* model — one that hesitates in the overlap band
(MMSE ~22, age ~65) instead of being confidently wrong. A label-in-disguise feature destroys that:
the model becomes confidently right *everywhere* because it is reading the answer key, and the
calibration lesson parked for Day 7 evaporates.

So the argument is not only about purity — it is about **purpose**. CDR would trivialize the exact
experiment being built.

---

## The decision (lean, not yet locked)
**Do not quietly drop CDR. Keep it as a labeled leakage baseline.**

- Train the primary model **without** CDR.
- Train a second "cheating" model **with** CDR.
- The accuracy gap between them **measures** the leakage.

For an evaluation project, the cheating model is not a mistake — it is an *instrument*. This serves
the mission better than a clean drop, and is more honest than a silent keep.

---

## What still has to happen before Day 6
1. Read the generator; confirm how `cdr` is drawn. Convert this from "in principle" to "decided."
2. (Optional, real numbers) Quantify CDR separation on the train split to see the effect empirically —
   supports the reasoning, does not replace the generator read.
3. Record the final keep-as-baseline / drop call in the Day 4 reflection.

## The honesty line (carried from Day 3)
Keep a clean boundary between **"the data shows"** and **"I already believed."** Right now this
document is mostly the second kind — reasoning and prior knowledge about what CDR *is*. The generator
read is what moves it toward the first.

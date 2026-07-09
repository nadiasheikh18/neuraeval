# Day 3 Reflection — July 9, 2026
*Generate first synthetic dataset (notebook authored Jul 2, run & committed Jul 9).*

## What I built
- 500-patient synthetic dataset, 17 columns (15 features + `patient_id` + `stage` label).
- Stratified 70/15/15 split → 350 / 75 / 75. Zero patient overlap; leakage assertion clean.
- Stage mix ~31/35/24/11 (target 30/35/25/10) — intentional imbalance intact.
- Data committed to repo root `data/` (commit b99b872).

## Reading the distributions (train split only)
**MMSE.** Mild and moderate overlap across the 15–25 range, but their *peaks* don't:
mild peaks ~20–25, moderate ~15–20. Overlapping ranges with separated class-conditional
means still carry real signal — a feature doesn't need clean separability to be useful.

**Age as a second axis.** Mild concentrates 50–70; moderate concentrates 60–80. Combining
MMSE + age separates the two better than either does alone. This is the core intuition
behind multivariate classification: two weak 1D separators can compose into one good 2D one.

## Where the model will struggle
The overlap band (e.g. MMSE ~22, age ~65) is exactly where errors will concentrate — not
random, but a predictable neighborhood where mild and moderate both plausibly fit. For
NeuraEval this is the point: an honest model should be *uncertain* there, not confidently
wrong. → revisit as a calibration question on Day 7.

## Caveat to keep honest
MMSE and age aren't fully independent — both are generated from `stage`, so they're
correlated *through* the label. Combining them helps, but not the full additive amount.

## Class imbalance
Stage 3 (severe) ≈ 11% of train — realistic minority class. Day 6: use
`class_weight='balanced'` in logistic regression.

## The CDR question (decide before Day 6)
CDR separates the stages almost perfectly — likely because clinicians partly *define* stage
using CDR. Open question: is CDR a legitimate feature, or the label wearing a disguise?
Resolve before training, or the model may "cheat."

## To verify on Day 4
- 2D scatter: MMSE vs age, colored by stage (train split only) — does the overlap shrink or persist?
- Explore the remaining feature distributions.

## What surprised me
- Severe cases peak at 70–80, not 80–90. Healthy cases thin out around 75 — within this
  sample, beyond ~75 most patients fall into some disease stage.
- Instinct: mild/moderate patients who survive past 80 probably don't die *from* the disease —
  late-life mortality is multifactorial (you die *with* it, not necessarily *from* it).

**Caveat — the real lesson:** these age patterns are what my *generator* produced, not
something in the world. Age is drawn from mean 62 → 67 → 71 → 75 by stage (std 8, clipped
40–95), so "severe peaks at 75" and "healthy ends near 75" are my own assumptions handed
back to me, not discovered biology. The dataset has no mortality/survival variable at all,
so the death observation is prior knowledge, not a data finding. Treat all of this as
hypotheses to test against real PPMI/ADNI data — keeping a clean line between "the data
shows" and "I already believed."

---

*Session Jul 9: ~2h active (ran cell-by-cell, read all source) / ~2h50m elapsed. Lunch break mid-session.*

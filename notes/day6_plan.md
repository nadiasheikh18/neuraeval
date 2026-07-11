# Day 6 Plan — Train First Classifier
*Runway written on Day 5 so Day 6 is execution, not decisions. Fit on train, select on val, test stays sealed.*

## Goal
A 4-class stage classifier (0 Healthy / 1 Mild / 2 Moderate / 3 Severe). First **honest baseline** — not a leaderboard chase.

## Feature set (decided on Day 4)
- **Drop:** `patient_id` (identifier, not a feature) and `stage` (the label).
- **Primary model excludes `cdr`.** Motor features (tremor, rigidity, bradykinesia, postural_instability, speech_problems) + MMSE carry the signal. Non-motor (depression, sleep_disorder, constipation) are weaker but keep them. `bmi`, `years_education`, `family_history` are near-null — keep for now, watch their coefficients, drop later if they only add variance.
- **Encode `sex`** (M/F → 0/1).

## The CDR leakage baseline (the instrument, not a mistake)
- **Model A — primary:** every feature **except `cdr`**.
- **Model B — "cheating":** same features **plus `cdr`**.
- The gap (B − A) in accuracy/F1 **measures the leakage `cdr` buys.** Report it as a number — this is a NeuraEval deliverable, not a footnote.

## Model
- Logistic regression with `class_weight='balanced'` (stage 3 is ~11% — don't let it vanish).
- Fit on **train** only. Make any choices on **val**. Do not touch **test** until the very end.

## Metrics (accuracy alone will lie on imbalanced 4-class)
- **Macro-F1** and **per-class** precision/recall — watch severe (minority) and the mild/moderate boundary.
- **Balanced accuracy.**
- **Confusion matrix** — on Day 4 you predicted errors concentrate in the MMSE ~20–25 / adjacent-stage band. Check whether the model agrees.

## Definition of done for Day 6
- Primary model trains, imbalance handled, CDR gap measured, and errors land roughly where Day 4 said they would.
- Calibration (is the model *uncertain* in the overlap band?) is **Day 7** — note the baseline now, don't chase it yet.

## Setup notes
- Notebook path fix is in (`DATA_DIR = '../data'`), so a fresh run writes to repo-root `data/`.
- Load `data/train.csv` (350) and `data/val.csv` (75); confirm shapes before modeling.

---

## FAQ

**Q: What does "honest baseline, not a leaderboard chase" mean?**

Day 6 builds the *first, simplest* model just to get a trustworthy reference point, not the best-possible model. You're not tuning to squeeze out the highest accuracy number (a "leaderboard chase," Kaggle-style). For an evaluation project, a model you *understand* beats one that scores high for reasons you can't explain.

**Q: What does "fit on train, select on val, test stays sealed" mean?**

The three-way split doing its job:
- **train (350)** — the data the model actually learns from.
- **val (75)** — data you check to *make decisions* (which features, which settings). The model doesn't learn from it; you use it to choose between options.
- **test (75)** — locked in a drawer until the very end, used *once* for an honest final score. Peek at it while building and you'll unconsciously tune toward it, and your final number becomes a lie. Same leakage discipline as Day 3.

**Q: What does `class_weight='balanced'` do?**

Severe is only ~11% of patients. A model chasing plain accuracy could just rarely predict "severe" and still look ~89% right — while being useless on the case that matters most. `balanced` makes errors on rare classes count more during training, forcing the model to take severe seriously instead of ignoring it.

**Q: What's the difference between logistic regression and linear regression?**

Same backbone, different output.

**Linear regression** predicts a *number* on a continuous scale — house price, temperature. It fits a line: `output = w·x + b`, and that raw value *is* the prediction (can be anything, negative to huge).

**Logistic regression** predicts a *probability of a category* — e.g., is this patient severe or not. It takes the same weighted sum (`w·x + b`), then runs it through the **sigmoid** function to squash it into 0–1. That probability becomes a class. So logistic = linear's core + a squashing step, trained to separate classes instead of fit a line.

Quick contrast:
- **Output:** linear → any number; logistic → probability (0–1) → a class label.
- **Job:** linear → regression (predict a quantity); logistic → classification (predict a category), despite the name.
- **Loss:** linear minimizes squared error; logistic minimizes log-loss (cross-entropy).

For NeuraEval: stage is a *category* (healthy/mild/moderate/severe), so you classify → logistic (the multi-class version is called softmax). You *could* run linear regression on the stage number 0–3, but it'd predict nonsense like "stage 2.4" and assume every gap between stages is equal — wrong tool for a label.

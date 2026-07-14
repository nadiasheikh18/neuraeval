# Day 6 Reflection — Jul 11, 2026
*Train the first classifier — an honest logistic-regression baseline. Fit on train, evaluate on val, test sealed. Below: the results, then the four questions worked through while interpreting them.*

## Results
| Model | Accuracy | Balanced acc | Macro-F1 |
|---|---|---|---|
| A — primary (no CDR) | 0.880 | 0.907 | 0.907 |
| B — cheating (with CDR) | 0.933 | 0.951 | 0.949 |

**Leakage gap (B − A):** +0.053 accuracy · +0.042 macro-F1.

**Confusion matrix, Model A (rows = true, cols = predicted):**
```
          H   Mi  Mo  Se
   H   [ 21   2   0   0 ]
   Mi  [  6  20   0   0 ]
   Mo  [  0   1  17   0 ]
   Se  [  0   0   0   8 ]
```

**Feature importance (mean |coef|):** mmse 1.65 · tremor 1.24 · speech 1.21 · bradykinesia 1.20 · postural 1.00 · rigidity 0.92 · sleep 0.42 · constipation 0.33 · depression 0.26 · age 0.26 · family_history 0.21 · sex 0.18 · bmi 0.13 · education 0.07

---

## Q1 — How does the confusion matrix help interpret Model A's errors?
Rows = true stage, columns = predicted. The diagonal is correct; off-diagonal is error, and its *position* shows which stages get confused.

- **Every error is between adjacent stages** (H↔Mi, Mo→Mi) — never a wild miss. The model learned the disease ordering, at worst it's "off by one."
- **Errors cluster at the Healthy/Mild boundary** — exactly the MMSE ~25–30 overlap band predicted on Day 4. The matrix is empirical proof of that intuition.
- **Severe is perfect (8/8)** — the minority class survived, thanks to `class_weight='balanced'`.
- **The error is asymmetric:** mild→healthy (6) far exceeds healthy→mild (2). The model under-detects mild disease — clinically the costlier direction.

The point: accuracy says "88% right"; the confusion matrix says *where* the 12% is wrong — all in the genuinely ambiguous zone, never in clear-cut cases. That's the right place to fail.

## Q2 — Is the model accurate confidently, or otherwise?
The confusion matrix shows *what* was predicted, not *how sure*. Pulling the predicted probabilities:

- Mean confidence when **correct**: 0.88 · when **wrong**: 0.74.
- **44% of wrong predictions were made at >0.80 confidence.**

So it's mixed — confident when right, but not reliably uncertain when wrong. A truly honest model would make every mistake near 0.5; this one doesn't. Some errors are honest (0.51 vs 0.49, true class close behind), but several are confidently wrong — worst case a **mild patient called healthy at 0.95 confidence** (the clinically dangerous direction).

Conclusion: the model is accurate but **not yet honestly calibrated** — confidence doesn't track correctness. That gap is exactly what Day 7 (calibration) exists to measure and fix, and it's the core reason NeuraEval matters: a confidently-wrong diagnostic model is worse than an uncertain one.

## Q3 — Why is the CDR leakage so modest (~5 points)?
The small gap is the right result — it confirms CDR is label-derived but not a clean oracle.

1. **Ceiling.** Model A is already at 0.88; MMSE + motor explain most of stage, leaving only 9 val errors for CDR to help with. Little room.
2. **Redundancy.** Everything here is stage-derived, so CDR echoes information MMSE and the motor scores already carry — diminishing returns, no new signal.
3. **The killer — CDR is ambiguous where the errors are.** CDR shares rungs between adjacent stages (H & Mi both 0.5; Mi & Mo both 1.0; Mo & Se both 2.0). The errors live at those boundaries, and a mild patient with CDR 0.5 looks identical to a healthy one — CDR is blurry exactly where it would need to be sharp.

Evidence: Model B fixed only 4 of the 9 errors (the boundary cases where CDR happened to land on an unambiguous value). That's also why CDR alone scored just 0.786 on Day 4 — the disguise has deliberate blur at the seams.

Punchline: on *synthetic* data the leak is small because the generator built the ambiguity in. On **real** ADNI/PPMI data, if clinicians used CDR to assign the label, the leak could be large and clean — that's where this cheating-baseline instrument earns its keep.

## Q4 — Why do age, depression, and sleep rank so low?
A low coefficient ≠ "unrelated to the disease." It means "adds little *once MMSE and the motor scores are already in the model*." Importance is relative to the other features.

- **Age:** shifts with stage (62→75) but std 8 swamps the ~4–5 year gaps (Day 4 overlap 0.679). It separates extremes, not adjacent stages — and motor already covers what it offers.
- **Depression / sleep:** they *do* rise with stage (depression 10→55%, sleep 15→70%), but they're noisy 0/1 flags — a depressed patient could be almost any stage. Continuous motor scores carry far more information per patient. Depression also plateaus (Mo=Se), so it can't split the top stages; sleep (0.42) beats depression (0.26) because its gradient is steeper.

So it's **redundancy + low information**, not irrelevance. Remove the motor features and these would jump up — importance is always conditional on what else is in the model.

**Honesty caveat:** this ranking is how the *generator* was wired (motor-dominant). In real Parkinson's, non-motor signs can be early or prodromal — REM sleep behavior disorder precedes motor symptoms by years. Don't carry "sleep/depression don't matter" over to real data; that's a fact about the synthetic rules, not the disease.

---

*Test still sealed — untouched. Calibration is Day 7.*

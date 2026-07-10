# Day 4 Reflection — July 10, 2026
*Explore feature distributions on the **train split only** (never touched test). Answer the*
*Day 3 cliffhanger (does MMSE+age shrink the mild/moderate overlap?) and quantify the CDR question.*
*Supersedes the reasoning-only note `day4_cdr_reasoning.md` — that file was the argument before*
*the numbers; this file is the argument after. Where they disagree, this one wins.*

Figures: `figures/scatter_mmse_age.png`, `figures/distributions_by_stage.png`.

---

## The one-line result
The mild/moderate overlap **persists** in 2D — age does not rescue it. CDR is **not** the
near-perfect oracle Day 3 assumed (0.786 alone, beaten by the other features at 0.891), but it
**is** the label in disguise: quantized onto the clinical CDR scale `{0, 0.5, 1, 2, 3}` with
shared boundaries. And the deeper point — in synthetic data, *every* feature is stage-derived,
so CDR is only the clinically obvious case of a general truth.

---

## Q1 — Does MMSE + age shrink the mild/moderate overlap? (No.)
Mild vs moderate, 5-fold CV accuracy (chance = 0.596):

| Feature set | CV accuracy |
|---|---|
| MMSE alone | **0.817** |
| Age alone  | 0.675 |
| MMSE + age | **0.808** (no gain; within noise, if anything lower) |

1D overlap coefficients: MMSE = 0.386 (moderate overlap), **age = 0.679** (near-total overlap).

**Reading it honestly.** Day 3's core intuition was: *"two weak 1D separators can compose into
one good 2D one … combining MMSE + age separates the two better than either alone."* On this
train split, **that is not what happened.** Age is a global gradient (healthy 61 → mild 67 →
moderate 72 → severe 73), so it helps at the *extremes* — but mild-vs-moderate is a 5-year gap
against an ~8.5-year spread, so for that specific hard boundary age adds essentially nothing.
The scatter shows it: the grey MMSE~20–25 band is a vertical smear of mild+moderate with no
separation by height.

The Day 3 caveat ("combining helps, but not the full additive amount") was right — and stronger
than written: for this pair, the additive amount is ~zero.

*Fair caveat on the caveat:* this is a **linear** classifier. Age could still contribute through
a nonlinearity or an interaction a logistic model can't see. Claim is "linear MMSE+age shows no
gain for mild/moderate," not "age is worthless everywhere" — it clearly separates healthy/severe.

## Q2 — Remaining feature distributions (train, by stage)
Mean by stage `[H, Mi, Mo, Se]`, monotonic trend noted:

- **Motor — all clean monotonic↑ signals:** tremor `[0.41,1.20,2.28,3.26]`, rigidity
  `[0.38,1.02,2.10,3.12]`, bradykinesia `[0.42,1.42,2.43,3.07]`, postural_instability
  `[0.39,0.95,1.84,2.81]`, speech_problems `[0.40,0.80,2.07,2.76]`.
- **Non-motor — weaker monotonic↑:** depression `[0.08,0.24,0.46,0.46]` (plateaus Mo=Se),
  sleep_disorder `[0.21,0.32,0.50,0.72]`, constipation `[0.21,0.33,0.51,0.67]`.
- **Near-null features (non-monotonic, ~flat):** bmi `[26.0,26.8,26.5,26.6]`,
  years_education `[13.7,13.5,13.1,13.5]`, family_history `[0.10,0.17,0.15,0.10]`.

Implication for Day 6: motor features carry most of the load; BMI/education/family_history are
close to noise and may cost more (variance) than they add. Don't expect them to earn their place.

## Q3 — The CDR verdict (this closes the Day 6 gate)
4-class, 5-fold CV accuracy (chance = 0.346):

| Feature set | CV accuracy |
|---|---|
| CDR alone | 0.786 |
| All 13 **non-CDR** features | **0.891** |

CDR value share within each stage:

```
cdr      0.0   0.5   1.0   2.0   3.0
stage0  0.87  0.13     -     -     -
stage1     -  0.61  0.39     -     -
stage2     -     -  0.52  0.48     -
stage3     -     -     -  0.46  0.54
```

**Two corrections to the Day 3 framing, then the call:**
1. CDR does **not** "separate almost perfectly." 0.786 alone, and it *loses* to the rest of the
   features. The near-perfect story was wrong.
2. But the mechanism is confirmed: values sit exactly on the canonical clinical CDR global scale
   with **shared rungs** between adjacent stages. That is `cdr = clinical_bucket(stage) + boundary
   noise`. The quantization is *why* it caps below perfect — deliberate boundary ambiguity — and
   simultaneously *proves* it is downstream of the label.

**The reframe that matters most:** the non-CDR set hitting 0.891 means the motor features are
*also* stage-derived. In this synthetic data, **everything is the label in disguise.** CDR is not
uniquely leaky — it is the clinically legible example of a property the whole dataset has by
construction. So the CDR leakage question is, on synthetic data, a **rehearsal**. It only bites
for real on ADNI/PPMI, where the live question is: *did the diagnostic protocol assign stage using
CDR?* If yes, a CDR→stage model reconstructs the clinician's arithmetic, not biology.

**Decision (carries to Day 6):** keep CDR as a **labeled leakage baseline**, not as a primary
feature. Train primary *without* CDR; train a second "cheating" model *with* it; the accuracy gap
measures the leakage. For an evaluation project the cheating model is an instrument, not a mistake.
The still-open confirmation is the generator read (below) — but the empirical fingerprint above is
already strong enough to act on.

---

## Still owed (small, cheap, before Day 6)
- **Generator read** — open the notebook, find where `cdr`, `mmse`, `age`, and the motor features
  are assigned from `stage`. Converts "the fingerprint says" into "I read the code." This is the
  last step that makes the CDR call fully mine, not Maui's.
- **Notebook path fix** still offered, not applied: `DATA_DIR = 'data'` → `'../data'`.

## The honesty line (carried from Day 3, and it earned its keep today)
Day 3 said: keep a clean boundary between *"the data shows"* and *"I already believed."* Today the
data overturned a Day 3 belief (age helps mild/moderate) and a Day 3 phrase (CDR is near-perfect).
Both were reasonable priors; both were wrong on contact with numbers. That is the method working,
not failing.

---

## Moana's synthesis — *(to write in your own words; this is the part that teaches)*
Prompts, not answers:
1. Age separates the extremes but not the adjacent pair. What does that tell you about which
   feature does the work at which boundary — and where the model's real uncertainty should live?
2. If *every* synthetic feature is stage-derived, what is a synthetic dataset actually good for
   testing, and what can it never tell you? (This is the NeuraEval thesis in one question.)
3. You predicted the CDR leak on Day 3 and the data confirmed the mechanism but not the magnitude.
   How do you want the "cheating baseline" to appear in the final evaluation — as a number, a plot,
   a sentence?

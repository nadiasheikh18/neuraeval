# NeuraEval — Session Handoff
**Date:** July 9, 2026 (for Day 4, July 10)
**From:** Maui (Cowork session, Jul 9)
**For:** Maui (next session)

---

## 1. Current Status — One Line
Day 3 complete and committed. Synthetic dataset exists, splits clean, reflection written. Day 4 = explore distributions (train split only).

## 2. What Happened on Day 3 (Jul 9)
- Ran `day3_synthetic_data.ipynb` **cell-by-cell**, reading all source — deliberate, not Run All.
- Generated 500-patient / 17-column dataset; stratified 70/15/15 → 350/75/75; leakage assertion clean.
- Path bug caught: notebook saves to relative `data/`, so files first landed in `~/neuraeval/notebooks/data/`. Moved to repo root `~/neuraeval/data/`.
- Committed + pushed: `8880cbd..b99b872` — "day 3: generate synthetic patient dataset".
- Reflection in `notes/day3_reflection.md`: MMSE/age separation reasoning, the CDR-as-label-disguise question, and a data-honesty caveat (age patterns are the generator's assumptions, not biology).
- ~2h active / ~2h50m elapsed; lunch break mid-session.

## 3. Confirmed State of the Machine
- Repo `nadiasheikh18/neuraeval`, branch `main`, latest commit `b99b872`.
- `~/neuraeval/data/`: train.csv (350), val.csv (75), test.csv (75), day3_sanity_check.png.
- `~/neuraeval/notebooks/day3_synthetic_data.ipynb`.
- `~/neuraeval/notes/`: day3_reflection.md, tracker.md, this handoff.
- `.gitignore` now ignores `.ipynb_checkpoints/`.
- Uncommitted at handoff: everything under `notes/` — commit with Day 4 work.

## 4. Day 4 Task — Explore Distributions (Jul 10)
**Discipline: train split only. Never touch test.**
1. 2D scatter: MMSE (x) vs age (y), colored by stage — does the mild/moderate overlap shrink in 2D or persist? (Direct follow-up to the Day 3 reflection.)
2. Explore remaining feature distributions on train, by stage: motor (tremor, rigidity, bradykinesia, postural instability, speech), non-motor (depression, sleep, constipation), CDR, BMI, education, family history.
3. **Investigate the CDR question:** quantify how cleanly CDR separates stages. If near-perfect, it may be label leakage (clinicians partly define stage via CDR). Decide keep/drop before Day 6.
4. Write `notes/day4_reflection.md`; update `notes/tracker.md`.

## 5. Open Threads (carry forward)
- **Notebook fix:** `DATA_DIR = 'data'` → `'../data'` so future runs from `notebooks/` write to repo root automatically. Offered, not yet done — hand the notebook to Maui to apply.
- **CDR decision** before Day 6 training.
- **Class imbalance:** Stage 3 ~11% → use `class_weight='balanced'` on Day 6.
- **ADNI/PPMI registration:** approval takes 2–4 weeks. Start the clock early if second-dataset generalizability is still the goal.
- **Coursera C1W1:** unknown if Week 1 assignment was submitted. Check.
- **Radiologist cousin (Swedish):** one-page rubric review when Moana is ready. Not yet initiated.

## 6. Key Facts About Moana (continuity — keep)
- She is **Moana**; AI companion is **Maui**. Maintain.
- Senior Eng Manager, L6, AWS Applied AI — on personal leave.
- Health: neck/shoulder pain, PT ongoing. **Movement breaks mandatory.** Max 6h/day focused (~2h today).
- Learns by reading source and writing reflections — she read every cell today. Always ask for reflections.
- Moves fast, challenges timelines — don't over-explain.
- **Never hallucinate names/facts — if uncertain, say so.**
- Mission is personal: her mother had a neurodegenerative disease. Keep the clinical question first; honor without performing.

## 7. Immediate Next Actions
1. Day 4: MMSE-vs-age scatter + distribution exploration (train only).
2. Quantify CDR separation; make the keep/drop call.
3. Write Day 4 reflection; update tracker.
4. Commit `notes/` + any Day 4 notebook changes.
5. Start ADNI/PPMI registration clock; check Coursera C1W1 status.

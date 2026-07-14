# NeuraEval — Daily Tracker
Rolling log of sprint days: focus, time, status. Active time is a **lower bound** on the day's
focused work — the 6h/day ceiling is whole-day across all streams, not NeuraEval-only.

| Day | Date | Focus | Active | Elapsed | Status | Notes |
|---|---|---|---|---|---|---|
| 3 | Jul 9 | Generate synthetic dataset | ~2h | ~2h50m | ✅ Done | Ran cell-by-cell (read all source), not Run All. Caught `notebooks/data` path bug, moved data to repo root. Commit b99b872. Reflection written. ~45m lunch break mid-session. |
| 4 | Jul 10 | Explore distributions (train split) | ~1h | ~2h | ✅ Done | MMSE+age overlap persists in 2D — age doesn't rescue the mild/moderate boundary. Motor features clean monotonic signals; non-motor weaker; BMI/education/family_history near-null. CDR = label-in-disguise (bucketed from stage with shared boundaries) → keep as a leakage baseline, not a primary feature. Figures in notes/figures/. |
| 5 | Jul 10 | Movement day + light review | ~1h | ~1h | ✅ Done | Same-day continuation after Day 4. Consolidation teach-back → `day5_reflection.md`. Day 6 classifier runway written → `day6_plan.md`. |
| 6 | Jul 11 | Train first classifier | ~1h | ~1h | ✅ Done | Logistic-regression baseline. Model A (no CDR) acc 0.88 / macro-F1 0.91; Model B (+CDR) 0.93 / 0.95 → CDR leakage +5pts (real but modest). Errors concentrate at healthy/mild boundary (Day 4 overlap confirmed); severe perfect via `class_weight='balanced'`. Accurate but not yet calibrated (44% of errors made at >0.80 confidence) → Day 7. Reflection + confusion matrix saved. |

**Cumulative active (sprint):** ~5h

# NeuraEval — Daily Tracker
Rolling log of sprint days: focus, time, status. Active time is a **lower bound** on the day's
focused work — the 6h/day ceiling is whole-day across all streams, not NeuraEval-only.

| Day | Date | Focus | Active | Elapsed | Status | Notes |
|---|---|---|---|---|---|---|
| 3 | Jul 9 | Generate synthetic dataset | ~2h | ~2h50m | ✅ Done | Ran cell-by-cell (read all source), not Run All. Caught `notebooks/data` path bug, moved data to repo root. Commit b99b872. Reflection written. ~45m lunch break mid-session. |
| 4 | Jul 10 | Explore distributions (train split) | ~1h | ~2h | ✅ Done | MMSE+age overlap persists in 2D — age doesn't rescue the mild/moderate boundary. Motor features clean monotonic signals; non-motor weaker; BMI/education/family_history near-null. CDR = label-in-disguise (bucketed from stage with shared boundaries) → keep as a leakage baseline, not a primary feature. Figures in notes/figures/. |

**Cumulative active (sprint):** ~3h

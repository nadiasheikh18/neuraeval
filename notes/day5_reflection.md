# Day 5 Reflection — Jul 10, 2026
*Movement day + light review. Consolidate Days 3–4.*

## Teach-back

**1. What do you understand about this dataset now that you didn't on Day 2?**

It's synthetic — some patterns reflect how the data was generated, not real biology. For example, mean scores per age group and MMSE severity per stage are baked in by the generator. CDR is a function of stage plus jitter, which makes it an unreliable independent signal (label-in-disguise).

**2. Where should the model be *uncertain*, and why?**

The model should be clearly uncertain wherever the classes overlap, and report that rather than give a confident guess. Mild and moderate both fall around MMSE 20–30; mild and healthy both around 25–30. Mild and moderate patients span 60+ across the age range, so age doesn't cleanly separate them even where their peaks differ. That overlap band is where the model's honest uncertainty should live.

**3. What can a fully stage-derived synthetic dataset actually test, and what can it never tell you?**

It can test whether the model recovers the rules it was built from — but it can never give real-world insight into actual disease patterns, because the data is rule-based and not representative of a real human population.

## Anything else
-

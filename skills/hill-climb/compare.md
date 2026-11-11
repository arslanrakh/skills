# Comparing versions

Criteria are never blended into one score. A candidate wins on the round's criterion and must be no worse on every other criterion.

Each criterion has one of three modes, named in the objective.

## Named cases

Named cases that must each hold, with a check that answers the same on a repeat.

- A finding counts once it is reproduced by action: a run, a measurement, a lookup in the source. A second model's opinion does not count.
- **Win:** at least one reproduced failing case now passes, and no case that passed before is broken.
- **No worse:** no case that passed before is broken.
- An attacker goes at the winner. A reproduced new finding cancels the win and joins the panel.
- Nothing is re-measured, because the check answers the same. In the re-check only the trimmed version is measured.
- A model may be the check when it compares a result with an expected answer and has passed the test of the measure (phase 1).

## Sample

A sample that stands for a larger set, or a measure with noise.

Measure both versions on the same examples. One line per example: `better`, `worse` or `same`. Drop the `same` lines; the rest are discordant. The table gives how many `better` are needed for that many discordant examples:

```
discordant : better needed          5 or fewer discordant: no conclusion
 6:6    7:7    8:8    9:8   10:9   11:10  12:10  13:11  14:12  15:12  16:13
17:13  18:14  19:15  20:15  21:16  22:17  23:17  24:18  25:18  26:19  27:20
28:20  29:21  30:21  31:22  32:23  33:23  34:24  35:24  36:25  37:25  38:26
39:27  40:27  41:28  42:28  43:29  44:29  45:30  46:31  47:31  48:32  49:32
50:33  51:33  52:34  53:35  54:35  55:36  56:36  57:37  58:37  59:38  60:39
above 60: half, plus the square root, plus one half; round up
```

- **Win:** `better` reaches the threshold.
- **Worse:** `worse` reaches the threshold.
- **Not proven means discarded.** A raw lead of a few examples is not a win.
- **Pass or fail.** `better`: the candidate passes an example the best version fails. `worse`: the reverse.
- **Magnitude** (a time, a score). `better` and `worse` count only when the difference exceeds the noise measured in phase 1. Versions are measured in alternating order. If `worse` reaches the threshold when the noise band is ignored, there is no win.
- **One example, measured by an instrument.** Alternating pairs of runs serve as the examples.
- **Re-check.** In a fresh measurement the winner must keep its direction: more `better` than `worse`. Report the fresh counts, never the first ones. If the measure answers the same on a repeat, the re-check of the pair is skipped.
- **A trimmed version is no worse than its parent** if there is no real break. Every example where it is worse is measured three more times for both. The parent failed even once: the example is unstable and does not count. The parent passed all three and the trimmed version failed even once: the break is real, and the trimmed version is discarded. If the measure answers the same on a repeat, any break is real.

What the table can show:

| Candidate | Examples | Wins and survives the re-check |
|---|---|---|
| fixes 30%, breaks 5% | 20 / 30 / 50 / 60 | 31% / 57% / 85% / 92% |
| fixes 15%, breaks 5% | 60 / 100 | 29% / 54% |
| equal to the best version | 60 | under 1% |

A sample under 30 examples decides nothing: enlarge it or hand the criterion to the user. When the best version fails fewer than six examples on a criterion, nothing can win there any more: take another criterion or add harder examples.

## Taste

A reader's impression. It cannot be written as a number.

- Models never pick the winner. The user or their readers do.
- Models find defects. A defect counts once it is reproduced by action: a broken layout, a wrong fact, a repeat.
- Between shows the loop fixes what can be checked, holds the checks the user has agreed to, and prepares finalists.
- On a taste criterion the best version changes only by the user's choice at a show that included the best version.

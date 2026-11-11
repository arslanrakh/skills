# hill-climb protocol

```
0 Objective → 1 Test the measure → the user's yes → 2 Wide start
  → 3 Round: start → two candidates → filter → measure → compare
             → deletion → re-check → decide → record
  → 4 Between rounds: show · widen when stuck · check against the past · audit · stop
  → 5 Finish
```

Every step leaves a trace in the state folder. No trace means the step was not done.

## What the environment must offer

1. **A fresh conversation:** start an agent that sees only the text and files you hand it.
2. **Files that outlive the session.**
3. **Several copies of the work side by side:** branches, worktrees, folders or duplicated documents.
4. **Ordinary tools:** run the work, count words, compare two copies.

Parallel starts, restricted file access, rendering and a source of randomness help when the environment has them. Without parallel starts, do the same things one after another.

The skill ships no scripts. A task whose panel is too large to run by hand gets one measuring procedure: the checker writes it in phase 1 (a command or a small script for this task), it lives in `checks/`, it is frozen after phase 1, and every measurement uses it.

## State

The folder `.hill-climb/<task>/` sits in a durable place in the project, outside every copy of the work: a generator never receives it with its copy.

| File | Holds |
|---|---|
| `objective.md` | the objective; its versions are numbered |
| `state.md` | where each named version lives, promotions since the last check against the past, rounds without promotion, shows without an answer. Rewritten every round |
| `panel.md` | the examples by name, how each is measured, how size is counted, the list of unstable examples |
| `checks/` | the measuring procedure, the checks, expected answers, held-out examples |
| `decisions.md` | the user's decisions verbatim; agents' decisions in a separate list |
| `ledger.md` | an entry for every candidate and every event; append only |
| `evidence/` | by round: briefs, cards, results with one line per example |

Named versions:

- `v0`: the starting version;
- `best`: the current best;
- `owner`: the last version the user approved; `v0` at the start;
- `prev`: the best version at the last check against the past;
- `alt-1` … `alt-3`: alternatives.

## Roles

| Role | Does |
|---|---|
| Main agent | manages: talks to the user, decides by this protocol, writes briefs, accepts what comes back, keeps the ledger. Does not do the work |
| Scout | studies the work and the field: what exists, who the competitors are, which examples fit the panel, which approaches differ, whether the objective can be met. Changes nothing |
| Generator | makes one candidate and its card; repairs it after the filter |
| Attacker | finds cases where a version fails the objective, with steps to reproduce; proposes no fixes |
| Checker | writes the measuring procedure and the known-bad versions; filters, measures, counts and applies `compare.md`, reproduces findings, examines an unexpected win |
| Auditor | reads the state folder and the versions it names and answers the audit questions |

Each call of a role is a fresh conversation. Whoever made a version does not measure it.

**You, the main agent, only manage.** You do six things:

1. talk to the user: the objective, the shows, the report;
2. decide by this protocol what happens next;
3. write briefs and start agents;
4. accept what comes back: is everything the template asks for there, and is the trace in the folder;
5. keep the ledger and `state.md`;
6. keep the run moving: no stop until the round-start questions say stop or wait.

You do not change the work, measure the panel, count results, hunt defects or research. A small fix is a generator's job too.

- **A brief** is the path to the role file, then the filled template. Save every brief to `evidence/<round>/`.
- **An agent failed.** Sharpen the brief and start a new one. After a second failure record it in the ledger and move on; if the round cannot go on without that step, tell the user.
- **Agents answer briefly:** the result and the paths. Raw output stays in `evidence/` and out of your conversation.
- Where the environment allows it, each round is led by a fresh main-agent conversation started from the folder.

## Phase 0. Objective

| № | Action | Trace |
|---|---|---|
| 0.1 | A scout studies the work and the field and proposes criteria, competitors and examples for the panel. From its report and the user's words write the objective by the template: what counts as winning and for whom, what it is compared against, the criteria. Order the criteria by reach: first the one the reader meets first | `objective.md` |
| 0.2 | Name the mode of every criterion (`compare.md`). **Named cases:** each must hold, and the check answers the same on a repeat. **Sample:** stands for a larger set, or the measure has noise. **Taste:** a reader's impression | `objective.md` |
| 0.3 | Name the panel, one for all versions. In a sample of 90 examples or more, every third in list order is held out until the end. Held-out examples live only in `checks/`; no copy of the work contains them | `panel.md` |
| 0.4 | Fix `v0` and the competitors | `state.md` |
| 0.5 | Set the work budget in rounds. Set the size budget in the unit the reader sees; by default it is the size of `v0`. Name the points where the user is shown versions; the first show comes no later than the end of round 1 | `objective.md` |

## Phase 1. Test the measure

| № | Action | Trace |
|---|---|---|
| 1.1 | The checker writes the measuring procedure and measures `v0` and the competitors with it | `checks/`, `evidence/0/` |
| 1.2 | The checker measures `v0` a second time. Examples whose outcome differs are recorded as unstable; their number is the noise of the panel. For a magnitude the noise is the largest difference between two measurements of one example | `panel.md` |
| 1.3 | The measure must catch bad work. The checker feeds it a version with a planted defect and a version padded with filler: the first must fail, the second must not score above `v0`. A known right answer must pass | ledger |
| 1.4 | On a named-cases criterion an attacker goes at `v0`. Reproduced findings join the panel | `panel.md` |
| 1.5 | Record what the panel can show: how many examples `v0` fails and what margin the table in `compare.md` demands. A sample under 30 examples decides nothing: enlarge it or hand the criterion to the user | ledger |
| 1.6 | The procedure, the checks and the expected answers are frozen. They change only by your decision, as a separate ledger entry; the checker makes the edit. If the environment can, record their checksum | `checks/`, ledger |
| 1.7 | Show the user the objective and what was learned about the measure. Work starts on their yes. This is the one question before the start | `decisions.md` |

## Phase 2. Wide start

| № | Action | Trace |
|---|---|---|
| 2.1 | A scout names 3 to 5 different approaches, one line each. One generator per approach builds it from scratch in the cheapest form | ledger, briefs |
| 2.2 | They and `v0` go through the filter, the measurement, the comparison and the re-check. The one that beat `v0` becomes `best`; among several, the one with the larger margin. Up to three that differ in approach stay as alternatives | ledger |
| 2.3 | On a taste criterion the user picks the direction from rendered variants, before the work is built in full | `decisions.md` |
| 2.4 | The phase is skipped when the user has fixed the approach | ledger: why |

## Phase 3. Round

| № | Action | Trace |
|---|---|---|
| 3.1 | **Start.** Re-read `objective.md`, `state.md` and the ledger from the files, not from memory. Answer the round-start questions in writing | ledger |
| 3.2 | **Criterion.** Take the first criterion by reach that has failures. Failures on taste are the user's words since their last yes. No failures anywhere: send an attacker at a named-cases criterion, add harder examples to a sample, prepare a show for taste. Nothing found: stop | ledger |
| 3.3 | **Candidates.** Two generators from `best`, each in its own copy and with its own 2 to 4 failing examples | copies |
| 3.4 | **Brief** by the template: the objective verbatim, the user's decisions, the failing examples with the raw result and the expected answer, what was already tried | `evidence/<round>/` |
| 3.5 | **Filter.** The checker looks: the work opens and passes its own checks; every addition is named in the card with what it closes; no verbatim piece of the panel sits in the work. A candidate that fails goes back to its generator, three times at most | ledger |
| 3.6 | **Measure.** The checker measures the whole working panel on every criterion for `best` and the candidates: in this round, with the frozen procedure, one line per example. Counting starts when the whole panel is measured; the checker counts | `evidence/<round>/` |
| 3.7 | **Compare.** The checker applies `compare.md`: a win on the round's criterion by its mode, no worse on every other criterion. You accept the verdict; the audit recounts it. Two winners: the one with the larger margin stays, and at an equal margin the smaller one | ledger |
| 3.8 | **Deletion.** A generator removes what the objective does not need from the winner, or from `best` when nobody won. Nothing the reader sees is added; joining edits are listed in the card. The trimmed version passes the same filter | copy |
| 3.9 | **Re-check.** A fresh measurement of three versions: `best`, the winner, the trimmed one. The winner must keep its direction. The trimmed version must be no worse than the version it was cut from. A win outside the prediction is examined by the checker; an unexplained win is not promoted | `evidence/<round>/` |
| 3.10 | **Decide.** The smaller of those that held is promoted: the trimmed version, otherwise the winner. First answer the promotion questions. A loser that differs in approach and beats `best` on at least one example stays as an alternative; a fourth pushes out the oldest. An idea that did no harm is recorded as an open hypothesis | ledger, `best` |
| 3.11 | **Record** an entry for every candidate. Rewrite `state.md` | `ledger.md`, `state.md` |

In a round on a taste criterion nobody wins before the show. The deletion is made from `best` alongside the candidates, and all of them go to the show.

## Phase 4. Between rounds

| № | Action | Trace |
|---|---|---|
| 4.1 | After a promotion comes a normal round from the new `best` | |
| 4.2 | After a round without a promotion comes a **wide round:** four generators. Two from `best`, one from the newest alternative with its failing examples, one from scratch. A candidate from an alternative is compared with `best`; if it beat only its parent, it takes the parent's place | ledger |
| 4.3 | One check fails candidates two rounds running: the checker checks the check, and a scout judges whether the objective can be met. If it cannot, tell the user | ledger |
| 4.4 | **Show.** The first comes no later than the end of round 1, later ones at the points named in the objective and whenever only taste remains. A show holds at most six versions: `best`; `owner` if it differs; 2 to 4 finalists, always including the shortest; one competitor; `v0` when a check against the past is due. Nothing is labelled. The order is random; without a source of randomness it shifts by one with every show. The key is written to the ledger before the show. The checker prepares the versions for the show | ledger |
| 4.5 | **A show does not stop the loop** while criteria other than taste remain: send it as a message and keep working. A promotion that changes what the reader sees is provisional until the user answers. No answer: go on with the other criteria and do not repeat the question. Only taste remains: end your turn with the show and wait | `state.md` |
| 4.6 | **The user's no:** the version or the change is discarded, and their words are recorded verbatim. You may phrase a check from them and read it back at once. On their yes the checker adds it to `checks/`. On their no there is no check. With no answer it is an agent's decision: a candidate may break it and says so in its card | `decisions.md` |
| 4.7 | **Check against the past** every three promotions: the checker measures `best` against `v0` and against `prev` on the whole panel, and old findings are run again. `best` is worse: roll back to that version. Losing to a competitor becomes the criterion of the next round. On a taste criterion the user makes this check at a show | ledger |
| 4.8 | **Audit** before every show, at every check against the past and at the end. A fresh agent gets the state folder and the versions it names and answers the audit questions. What it finds is fixed before the next round | ledger |
| 4.9 | **Stop:** two wide rounds in a row without a promotion, or the budget is spent, or there is nowhere left to look (step 3.2). The best version stays, not the latest | ledger |
| 4.10 | **The objective changes** only on the user's yes: a new numbered version. If criteria or examples changed, the panel and the checks are rebuilt, `best` is measured again, and earlier comparisons are marked incomparable | `objective.md` |

## Phase 5. Finish

| № | Action | Trace |
|---|---|---|
| 5.1 | The checker measures `best` against `v0` on the held-out examples, once. Three outcomes: confirmed by the table; same direction but not proven; reversed, and then the version is not declared better and the user decides. No held-out examples: say so in the report | ledger |
| 5.2 | Report to the user: what was kept and what was discarded; what was added and removed against `v0`; the taste calls; open hypotheses; the cost; what was not verified | |

## Templates

**Objective**

```
# Objective, version 1
Winning: <what changes> for <whom>
Compared against: v0 = <where>; competitors: <who>
Criteria by reach:
  1. <criterion>: <named cases | sample | taste>, judged by <what or who>
  2. …
Panel: <N> examples, panel.md; held out: <how many, or "not split">
Work budget: <rounds>
Size budget: <number> <unit the reader sees>; by default the size of v0
Shows: the first no later than the end of round 1; then <when>
```

**state.md**

```
objective: v<№>            round: <№>
v0: <where>   best: <where>   owner: <where>   prev: <where>   alt-1..3: <where>
promotions since the last check against the past: <N>
rounds without promotion: <N>, of them wide in a row: <N>
shows without an answer: <which>      provisional promotions: <which>
open hypotheses: <list>
```

**Brief to any agent**

```
Read <skill folder>/roles/<role>.md first.
<the fields below for that role>
Return only: <what the role file asks for>.
```

**Generator brief**

```
Objective, verbatim: <objective.md in full>
The user's decisions, not to be changed: <from decisions.md>
Agents' decisions, open to challenge: <from decisions.md>
Type: <refine | delete | from scratch | from an alternative | repair>
Starting version: <where>. Work only in your copy: <where>.
Criterion of the round: <one>
Failing examples: <2 to 4: what went in, what came out, what was expected, what the check said>
Names of all examples failing on this criterion: <list>
Already tried, and why it was discarded: <from the ledger>
Open hypotheses: <from the ledger>
```

A deletion brief drops the three lines about failures.

**Checker briefs**

```
Job: filter. Candidate: <where>. Parent version: <where>. Card: <text>.

Job: measure. Versions: <where each is>. Panel: panel.md. Procedure: checks/.
Criterion of the round: <one>. Write results to evidence/<round>/.

Job: reproduce. Finding: <text>. Version: <where>.

Job: examine a win. Winner: <where>. Its prediction: <text>. Results: <path>.
```

**Attacker brief**

```
Objective, verbatim: <objective.md>. Version: <where>. Known cases, do not repeat: <list>.
```

**Ledger entries**

```
## Round <№> · objective v<№> · candidate <name> · <type> · from <version>
Hypothesis: …
Added: … (closes …)      Removed: …
Prediction: …
Size: <before> → <after>      Cost: …
Filter: <passed | what failed, how many repairs>
Measured: better <N>, worse <M> on the round's criterion; other criteria: <no worse | what is worse>
Re-check: better <N>, worse <M>; outside the prediction: <what the checker found>
Decision: <promoted | promoted provisionally | discarded | alternative>, because …

## Event · <show № | check against the past | audit | panel or check changed | stop>
What was done, what came back, what changed.
```

**Round-start questions.** Answer in order; the first yes decides.

```
1. Is the budget spent, or were the last two wide rounds without a promotion?  → stop
2. Does only taste remain, with a show unanswered?                             → wait
3. Is a show due?            → show; the round still runs if a criterion other than taste remains
4. Three promotions since the last check against the past?   → that check, and an audit
5. Has one check failed candidates two rounds running?       → check the check
6. Did the last round end without a promotion?               → wide round
7. Does the best version fail fewer than six examples on a sample criterion?
                             → the criterion is spent: another criterion, or harder examples
```

**Promotion questions**

```
Win recorded: same panel, same round, the round's criterion?
    <yes | the user's choice at a show that included best>
No worse on every other criterion?                    <yes | no>
Re-check: did the direction hold?   <yes | no | the measure answers the same | the user's choice>
Filter passed, and every addition names what it closes?   <yes | no>
Size inside the budget?                               <yes | no: needs the user's yes>
Does it change what the reader sees before the user has seen it?   <yes: the promotion is provisional>
Any no: no promotion.
```

**Show**

```
Show <№> · criterion: <one>
Up to six versions under letters, unlabelled. With each: the rendered view and its size.
Separately: what was added and what was removed since the last approved version.
Question: which one stays? One word about the others is enough.
```

Record the answer verbatim, then reveal the key.

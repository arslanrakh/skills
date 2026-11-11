# hill-climb protocol

```
0 Objective → 1 Test the measure → the user's yes → 2 Wide start
  → 3 Round: start → two candidates → check → combine → filter → attack
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
| `state.md` | where each named version lives, wins since the last check against the past, rounds without a win, shows without an answer. Rewritten every round |
| `panel.md` | the examples by name, how each is measured, how size is counted, the list of unstable examples |
| `checks/` | the measuring procedure, the checks, expected answers, held-out examples |
| `decisions.md` | the user's decisions verbatim; agents' decisions in a separate list |
| `ledger.md` | an entry for every candidate and every event; append only |
| `evidence/` | by round: cards, results with one line per example |

Beside the copies of the work sits `briefs/`: every brief, and a copy of the current objective that briefs point to. It is the one part of the record a generator may read.

Named versions:

- `v0`: the starting version;
- `best`: the current best;
- `owner`: the last version the user approved; `v0` at the start;
- `prev`: the best version at the last check against the past;
- `alt-1` … `alt-3`: alternatives.

## Roles

| Role | Does |
|---|---|
| Main agent | manages: talks to the user, decides by this protocol, writes briefs, accepts what comes back, keeps the record. Does not do the work |
| Scout | studies the work and the field: what exists, who the competitors are, which examples fit the panel, which approaches differ, whether the objective can be met. Changes nothing |
| Generator | makes one candidate and its card; repairs it after the filter |
| Attacker | finds cases where a version fails the objective, with steps to reproduce; proposes no fixes |
| Checker | writes the measuring procedure and the known-bad versions; measures, counts and applies `compare.md`, filters, reproduces findings, examines an unexpected win |
| Reader | sees only what a first-time reader sees and answers fixed questions about it; the checker compares the answers with the expected ones |
| Auditor | reads the state folder and the versions it names and answers the audit questions |

Each call of a role is a fresh conversation. Whoever made a version does not measure it.

**You, the main agent, only manage.** You do six things:

1. talk to the user: the objective, the shows, the report;
2. decide by this protocol what happens next;
3. write briefs and start agents;
4. accept what comes back: is everything the template asks for there, and is the trace in the folder;
5. keep the record: the ledger and `state.md`;
6. keep the run moving: no stop until the round-start questions say stop or wait.

You do not change the work, measure the panel, count results, hunt defects or research. A one-line fix is a generator's job too. You may read the work enough to write a brief; studying it is the scout's job.

Record keeping is yours: pinning a version (a commit, a tag, a copy), restoring a copy to its recorded state, moving stray files into the state folder, assembling a show from versions and sizes already measured, putting a version the user approved into the work. Changing what a version says or does is never yours. If you catch yourself reading results to pick examples or to count, ask the checker for that list instead.

- **A brief** is a file in `briefs/`: the path to the role file, then the filled template. Give the agent the path of its brief. It points to the objective by path and never retells it.
- **An agent failed.** Sharpen the brief and start a new one. After a second failure record it in the ledger and move on; if the round cannot go on without that step, tell the user.
- **Agents answer briefly:** each writes its card or result to the path its brief names and returns that path with a one-line verdict. A generator's card goes beside its brief; everything else goes to `evidence/`. Check that the file is there; do not retype it.
- **Role agents start no agents of their own.** A checker that needs to read a text reads it itself. When a case asks what a first-time reader understands, you start a reader at the checker's request.
- **Spend agents where they can change a decision.** One call may carry several mechanical steps. A step that cannot change any decision is skipped and recorded as skipped.

## Phase 0. Objective

| № | Action | Trace |
|---|---|---|
| 0.1 | A scout studies the work and the field and proposes criteria, competitors and examples for the panel. From its report and the user's words write the objective by the template: what counts as winning and for whom, what it is compared against, the criteria. Name every reader of the work: who uses its result and who maintains or edits it. Order the criteria by reach: first the one the reader meets first. A criterion the scout names that only taste can judge is not dropped: it goes into the question of step 1.7, to include or to leave out | `objective.md` |
| 0.2 | Name the mode of every criterion (`compare.md`). **Named cases:** each must hold, and the check answers the same on a repeat. **Sample:** stands for a larger set, or the measure has noise. **Taste:** a reader's impression | `objective.md` |
| 0.3 | Name the panel, one for all versions: the scout proposes the examples and their expected answers, and the checker writes them down. In a sample of 90 examples or more, a third is held out until the end: every third in list order, or whole groups when examples share a source or a template. Held-out examples live only in `checks/`. Nothing from them, not even a name, appears in a copy of the work, the objective, a brief or a card; a copy carries no version history that holds them, so use plain folders or a fresh repository made without them. Generators see examples only through their brief: panel data that sits inside the work (a file of examples, a test file) is taken out of the copies, and if it cannot be, say so in step 1.7 | `panel.md` |
| 0.4 | Fix `v0` and the competitors | `state.md` |
| 0.5 | Set the work budget in rounds; the wide start is not counted as one. For small work propose a narrow run: a wide start of three and one generator a round. Propose the size budget in the unit the reader sees: the size of `v0`; less when the objective is about brevity, by the size of the competitors; more only when `v0` plainly cannot meet the objective at its size, and say why. Count size so that deleting blank lines, comments or explanations does not lower it. The user confirms the budget in step 1.7. Name the points where the user is shown versions; the first show comes no later than the end of round 1 | `objective.md` |

## Phase 1. Test the measure

| № | Action | Trace |
|---|---|---|
| 1.1 | The checker writes the measuring procedure and measures `v0` and the competitors with it | `checks/`, `evidence/0/` |
| 1.2 | The checker measures `v0` a second time. Examples whose outcome differs are recorded as unstable; their number is the noise of the panel. For a magnitude, it runs `v0` against itself in alternating pairs and records how far two runs differ | `panel.md` |
| 1.3 | The measure must catch bad work. The checker feeds it a version with a planted defect and a version padded with filler. The first must fail: a named case fails, or a sample comes out worse than `v0` by the table. The second must not score above `v0`. A known right answer must pass, and it must look like a plausible improved version, not like `v0` | ledger |
| 1.4 | An attacker goes at `v0`, unless every named case is defined as what `v0` does. A reproduced finding that shows a stated requirement broken joins the panel as a named case. A finding that is one more example of a behaviour judged by a count is kept as a hard example for the briefs and joins no panel (`compare.md`) | `panel.md` |
| 1.5 | Record what the panel can show: how many examples `v0` fails and what margin the table in `compare.md` demands. A sample under 30 examples decides nothing: enlarge it or hand the criterion to the user. When a plain honest version would pass every named case, the cases will decide nothing after the first rewrite: say so to the user, and build two approaches at most in the wide start | ledger |
| 1.6 | The procedure, the checks and the expected answers are frozen. They change only by your decision, as a separate ledger entry; the checker makes the edit. If the environment can, record their checksum | `checks/`, ledger |
| 1.7 | Show the user the objective, what was learned about the measure, and the budgets you propose. Work starts on their yes. This is the one question before the start | `decisions.md` |

## Phase 2. Wide start

| № | Action | Trace |
|---|---|---|
| 2.1 | A scout names 3 to 5 different approaches, one line each. One generator per approach builds it from scratch in the cheapest form | ledger, briefs |
| 2.2 | They and `v0` go through one round check. Rank the winners on that check: the larger margin first, then the smaller size. Only the first goes on to the filter, the attack and the deletion; if its win is cancelled, the next one does. It becomes `best`. Up to three others that differ in approach stay as alternatives, untrimmed and unattacked | ledger |
| 2.3 | On a taste criterion they go through the check and the filter only. The user picks the direction from rendered variants, before the work is built in full, and the run waits for that pick. Nobody becomes `best` before it, and nobody is attacked or trimmed | `decisions.md` |
| 2.4 | The phase is skipped when the user has fixed the approach | ledger: why |

## Phase 3. Round

| № | Action | Trace |
|---|---|---|
| 3.1 | **Start.** Re-read `objective.md`, `state.md` and the ledger from the files, not from memory. Answer the round-start questions in writing | ledger |
| 3.2 | **Criterion.** Take the first criterion by reach that has failures. Failures on taste are the user's words since their last yes. A magnitude has failures only while it falls short of what the objective calls enough. No failures anywhere: send an attacker at the named-cases criteria, have a scout write harder examples for a sample from the objective and its sources, prepare a show for taste. Nothing found: stop; no round is run for a deletion alone | ledger |
| 3.3 | **Candidates.** Two generators from `best`, each in its own copy and with its own 2 to 4 failing examples of one kind. With fewer failures, split them; with a single failure both get it, each with a different direction. When the ledger holds open hypotheses, one of the two combines them instead | copies |
| 3.4 | **Brief** by the template: the objective by path, the user's decisions, the failing examples with the raw result and the expected answer, what was already tried | `briefs/` |
| 3.5 | **Check.** A checker opens each candidate and runs its own checks, then measures the whole working panel on every criterion for `best` and the candidates: in this round, with the frozen procedure, one line per example. It counts when the whole panel is measured and applies `compare.md`: a win on the round's criterion by its mode, no worse on every other criterion | `evidence/<round>/` |
| 3.6 | **Combine.** Two or more candidates gained with nothing broken, on different examples or, where the criterion has one example, by changes to different parts of the work, whether or not one of them won: a generator combines them into one candidate, and the checker measures it like the others. If they rewrite the same text, name in the brief whose text stays and what of the other is carried into it, or skip the combine and record why | ledger |
| 3.7 | **Rank.** Several winners are ranked on the panel as it stood at the round's check. The round's criterion decides; at an equal margin the next criterion by reach, then the smaller size. A tie in all of them goes to the user at the next show; do not break it by argument. Until then the tied versions go on side by side, one generator from each | ledger |
| 3.8 | **Filter the winner.** The checker compares it with its parent: every addition is named in the card with what it closes, and no panel input sits in the work together with its expected answer, comments included. A failure goes back to the generator that made it, in the same conversation where the environment can continue it, three times at most, and the repaired version is measured again. A candidate that did not win is not filtered and not repaired | ledger |
| 3.9 | **Attack the winner** when the objective has a named-cases criterion. A reproduced finding that shows a stated requirement broken joins the panel and is measured on `best` and the winner alike. If it breaks the winner and not `best`, it cancels the win, and the next winner by the ranking takes its place, once. If both fail it, it is work for the next round. Where the source does not state the expected answer, the case is an agent's decision: listed in `decisions.md` and read back at the next show. Examples of a behaviour judged by a count cancel nothing: they go into the next round's briefs. Cases added by an attack do not change the objective; the ledger records how the panel grew | `panel.md` |
| 3.10 | **Deletion.** A generator removes what no named reader uses from a winner that survived the attack, or from `best` when nobody won. Nothing the reader sees is added; joining edits are listed in the card. The checker filters the trimmed version too | copy |
| 3.11 | **Re-check.** A fresh measurement of `best`, the winner and the trimmed version; when the measure answers the same on a repeat, only the trimmed version is measured. The winner must keep its direction. The trimmed version must be no worse than the version it was cut from. A win outside the prediction is examined by the checker; an unexplained win is not promoted | `evidence/<round>/` |
| 3.12 | **Decide.** Answer the promotion questions. A winner that held is promoted in its smaller form: the trimmed version if that is no worse, otherwise the winner itself. A trim that removes text the reader uses and the panel cannot see is not promoted: it goes to the show as a separate question. Nobody won and the trimmed `best` is no worse: it replaces `best` as a trim, which is not a win and does not count in the round-start questions. A loser or a cancelled winner that differs in approach and beats `best` on at least one example stays as an alternative; a fourth pushes out the oldest. An idea that gained and broke nothing is recorded as an open hypothesis | ledger, `best` |
| 3.13 | **Record** an entry for every candidate. Rewrite `state.md` | `ledger.md`, `state.md` |

When the procedure runs in seconds, a round takes two checker calls. The first checks every candidate and filters the winner. The second reproduces the attack findings, edits the checks, measures the trimmed version and filters it.

In a round on a taste criterion nobody wins before the show. The deletion is made from `best` alongside the candidates, and all of them go to the show. The checker does not measure the whole panel for them: in one call it confirms that no named case that `best` passes is broken, and filters them. When the objective has a named-cases criterion, an attacker goes at the finalists before the show, and a reproduced finding is stated at the show next to the version it breaks. Once every finalist passes every named case, the cases decide nothing more: say so to the user, and keep the rounds in this form.

## Phase 4. Between rounds

| № | Action | Trace |
|---|---|---|
| 4.1 | After a win comes a normal round from the new `best` | |
| 4.2 | After a round without a win, or a wide start without one, comes a **wide round:** four generators. Two from `best`, one from the newest alternative with its failing examples, one from scratch. A candidate from an alternative is compared with `best`; if it beat only its parent, it takes the parent's place | ledger |
| 4.3 | **Check the check.** One check fails candidates two rounds running, the wide start included: the checker checks the check, and a scout judges whether the objective can be met. If the check or the objective stands in the way, tell the user and wait; do not run another round under it | ledger |
| 4.4 | **Show.** The first comes no later than the end of round 1, later ones at the points named in the objective and whenever only taste remains. You assemble it from what is already measured; a checker prepares it only when the versions must be rendered. The key is written to the ledger before the show | ledger |
| 4.5 | **A show on a taste criterion** holds at most six versions: `best`; `owner` if it differs; 2 to 4 finalists, always including the shortest candidate, even when it breaks a named case (say which); one competitor, when it does the same job; `v0` when a check against the past is due and at the last show of the budget. Nothing is labelled. The order is random; without a source of randomness it shifts by one with every show | ledger |
| 4.6 | **A show on other criteria** holds `best` against `owner` and one competitor that does the same job; the runner-up when it differs from `best` in something the checks cannot see; up to three alternatives while `best` is still `owner`; what was added and removed; and what the checks do not decide: behaviour or content that differs between versions and is not on the panel | ledger |
| 4.7 | **A show does not stop the loop** while criteria other than taste remain: send it as a message and keep working. The user may answer in the middle of the run or after your turn ends; take the answer whenever it comes and do not repeat the question. Do not count on a mid-run show being read before your turn ends. A win that changes what the reader sees is provisional until the user answers. Only taste remains: end your turn with the show and wait | `state.md` |
| 4.8 | **The answer changes `best` in the middle of a round.** Candidates made from the old `best` are not promoted. Each of their edits that gained and broke nothing becomes an open hypothesis for a combine on the new `best`, and generators not yet started begin from it. With the budget spent, offer one more round for that | ledger |
| 4.9 | **The user's no:** the version or the change is discarded, and their words are recorded verbatim. You may phrase a check from them. The checker writes the rule as it will be run, and you read that exact rule back in your next message to the user. On their yes it joins `checks/`, and the objective gets a new version (step 4.13). On their no there is no check. With no answer it is an agent's decision: a candidate may break it and says so in its card | `decisions.md` |
| 4.10 | **Check against the past** every three wins: the checker measures `best` against `v0` and against `prev` on the whole panel, and old findings are run again. `best` is worse: roll back to that version. Losing to a competitor becomes the criterion of the next round. On a taste criterion the user makes this check at a show. A pick at a show is not counted as a win. The check is not run after a stop: the finish measures `best` against `v0` | ledger |
| 4.11 | **Audit** before the first show is prepared, at every check against the past and at the end; skipped when nothing has replaced `best` since the last one. A fresh agent gets the state folder and the versions it names and answers the audit questions. What it finds is fixed before the next round | ledger |
| 4.12 | **Stop:** two wide rounds in a row without a win, or the budget is spent, or there is nowhere left to look (step 3.2). The best version stays, not the latest | ledger |
| 4.13 | **The objective changes** only on the user's yes: a new numbered version. A case that joins the panel under an existing criterion is not a change of objective. If criteria changed, the panel and the checks are updated, `best` is measured again, and earlier comparisons are marked incomparable | `objective.md` |

## Phase 5. Finish

| № | Action | Trace |
|---|---|---|
| 5.1 | When the objective has a named-cases criterion, an attacker goes at the final `best`, unless it was attacked in its own round and has not changed since. With the budget spent, reproduced findings are reported, not fixed | ledger |
| 5.2 | The checker measures `best` against `v0` on the held-out examples, once. Three outcomes: confirmed by the table; same direction but not proven; reversed, and then the version is not declared better and the user decides. No held-out examples: say so in the report | ledger |
| 5.3 | Report to the user: what was kept and what was discarded; what was added and removed against `v0`; the taste calls; open hypotheses; the cost; what was not verified | |
| 5.4 | On the user's word the final version is put into the work. That is record keeping: the version is copied as it was approved, with no edit. Committing or publishing it is the user's call | ledger |

## Templates

**Objective**

```
# Objective, version 1
Winning: <what changes> for <whom>
Readers: <who uses the result; who maintains or edits it>
Compared against: v0 = <where>; competitors: <who>
Criteria by reach:
  1. <criterion>: <named cases | sample | taste>, judged by <what or who>
  2. …
Panel: <N> examples; held out: <how many, or "not split">
Work budget: <rounds>; width: <two generators a round | narrow: one>
Size budget: <number> <unit the reader sees; in code, statements>
Enough: <for each magnitude: the value that is good enough, and the smallest gain worth a round>
Shows: the first no later than the end of round 1; then <when>
```

**state.md**

```
objective: v<№>            round: <№>
v0: <where>   best: <where>   owner: <where>   prev: <where>   alt-1..3: <where>
wins since the last check against the past: <N>
rounds without a win: <N>, of them wide in a row: <N>
shows without an answer: <which>      provisional wins: <which>
open hypotheses: <list>
```

**Brief to any agent**

```
Read <skill folder>/roles/<role>.md first.
<the fields below for that role>
Work and scratch files only in: <where>.
Run everything in the foreground; do not end your turn while a measurement is pending.
Write your card or result to: <path>.
Return only: <what the role file asks for>.
```

**Generator brief**

```
Objective: <path of its copy in briefs/>
The user's decisions, not to be changed: <from decisions.md>
Agents' decisions, open to challenge: <from decisions.md>
Type: <refine | combine | delete | from scratch | from an alternative | repair>
Starting version: <where>. Work only in your copy: <where>.
Criterion of the round: <one>
Failing examples: <2 to 4 of one kind: what went in, what came out, what was expected, what the check said>
Failures left on this criterion: <how many, by kind>
Already tried, and why it was discarded: <from the ledger>
Open hypotheses: <from the ledger; for a combine, which ones to apply together>
```

A deletion brief drops the lines about failures.

**Checker briefs**

```
Job: round check. Versions: <where each is>, cards: <paths>. Panel: panel.md. Procedure: checks/.
Criterion of the round: <one>. Write results to evidence/<round>/.

Job: filter. Candidate: <where>. Parent version: <where>. Card: <text>.

Job: reproduce. Finding: <text>. Version: <where>.

Job: examine a win. Winner: <where>. Its prediction: <text>. Results: <path>.

Job: prepare a show. Versions: <where each is>. Write the show to evidence/<round>/.
```

**Attacker brief**

```
Objective: <path of its copy in briefs/>. Version: <where>. Known cases, do not repeat: <list>.
```

**Reader brief**

```
What the reader sees: <the text or view, cut where the case cuts it>
Questions: <the fixed questions of the case>
```

**Ledger entries.** Leave out the lines that do not apply.

```
## Round <№> · objective v<№> · candidate <name> · <type> · from <version>
Hypothesis: …
Added: … (closes …)      Removed: …
Prediction: …
Size: <before> → <after>      Cost: …
Measured: better <N>, worse <M> on the round's criterion; other criteria: <no worse | what is worse>
Filter: <passed | what failed, how many repairs | not run: did not win>
Attack: <findings reproduced, and whether any breaks only this version>
Re-check: better <N>, worse <M>; outside the prediction: <what the checker found>
Decision: <promoted | promoted provisionally | trim | discarded | alternative | open hypothesis>, because …

## Event · <show № | check against the past | audit | panel or check changed | stop>
What was done, what came back, what changed.
```

**Round-start questions.** Answer in order; the first yes decides.

```
1. Is the budget spent, or were the last two wide rounds without a win?   → stop
2. Does only taste remain, with a show unanswered?                        → wait
3. Is a show due?            → show; the round still runs if a criterion other than taste remains
4. Three wins since the last check against the past?   → that check, and an audit
5. Has one check failed candidates two rounds running, the wide start included?
                             → check the check; if it stands in the way, tell the user and wait
6. Did the last round, or the wide start, end without a win?   → wide round
7. On a magnitude criterion, is enough met, or did the last win gain less than the smallest
   gain worth a round?   → the criterion is spent
8. On a sample criterion, does the best version fail fewer than six examples, or do its
   failures fall into kinds of fewer than six each?
                             → combine the open hypotheses or go from scratch;
                               with fewer than six failures in all, the criterion is spent
```

**Promotion questions**

```
What is promoted, and on what ground?
    <a win on the round's criterion, same panel, same round
     | the user's choice at a show that included best
     | a trim: nobody won, and the trimmed best is no worse>
No worse on every other criterion?                    <yes | no>
Re-check: did the direction hold?   <yes | no | the measure answers the same | not a win>
Filter passed, and every addition names what it closes?   <yes | no>
Attack: no reproduced finding breaks this version alone?  <yes | no | no named-cases criterion>
Size inside the budget?                               <yes | no: needs the user's yes>
Does it change what the reader sees before the user has seen it?   <yes: provisional>
Any no: nothing replaces best.
```

**Show**

```
Show <№> · criterion: <one>
Taste: up to six versions under letters, unlabelled; with each, the rendered view and its size.
For code, the view is a table of each version's results on the inputs where they differ.
Other criteria: best against the last approved version and a competitor; results on the panel;
  what differs and is not on the panel.
Separately: what was added and what was removed since the last approved version.
Question: which one stays, or what is wrong? One word about the others is enough.
```

Record the answer verbatim, then reveal the key.

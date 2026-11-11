# Three runs

These are invented. They show the order of work, not results.

## 1. Sample: a prompt that sorts support tickets into categories

**Objective.** The right category for last quarter's tickets, for the support team. One criterion: accuracy, mode sample. Size budget: 410 words, the size of `v0`. Work budget: six rounds.

**Panel.** 90 tickets with a category checked by a person. Every third is held out: 60 working, 30 for the end.

**Test of the measure.**

- The procedure: a command feeds the model the prompt and a ticket and compares the category with the expected one. It lives in `checks/`.
- `v0` measured twice: 43 and 44 of 60. Three tickets differed and are recorded as unstable.
- A prompt without the category list scored 9 of 60. A prompt padded with filler scored 43, not above `v0`.
- `v0` fails 17 examples, so the table has room to work.
- The user: "yes".

**Wide start.** Three approaches from scratch. The best one, "first the gist of the request, then the category": better 9, worse 5. Discordant 14, the table needs 12. Not proven: `v0` stays `best`, and the approach is kept as `alt-1`.

**Round 1.** Nine of the 17 failures confuse "refund" with "wrong charge". Generator 1a gets three such tickets with the model's answer and the expected category; generator 1b gets three other failures.

| Candidate | What it added and what that closes | Better / worse | By the table |
|---|---|---|---|
| 1a | a rule: "charged with no order" against "had an order, wants the money back"; closes the refund and charge confusion | 9 / 1 | of 10 it needs 9: win |
| 1b | sharper descriptions of two categories; closes the delivery and order-status confusion | 5 / 3 | of 8 it needs 8: not proven |

Deletion from 1a: 6 of 15 points struck out, 455 → 281 words.

Re-check, a fresh measurement of three versions:

- `v0` scored 44.
- 1a against `v0`: better 8, worse 1. The direction held. Seven of the fixed tickets were predicted; the eighth is also about a charge, worded differently.
- The trimmed version against 1a: worse on tickets 8 and 31, better on one. Both were measured three more times. On ticket 8, 1a itself failed once: unstable. On ticket 31 both passed three times. No real break.

```
## Round 1 · objective v1 · candidate 1a · refine · from v0
Hypothesis: the model confuses a refund with a wrong charge; it needs a rule that tells them apart.
Added: one rule, 45 words (closes 9 failures with this confusion)      Removed: nothing
Prediction: tickets 3, 7, 12, 19, 23, 31, 40, 44, 52 will be fixed; the rest will not change.
Size: 410 → 455 words      Cost: none
Measured: better 9, worse 1; no other criteria
Filter: passed
Re-check: better 8, worse 1; outside the prediction one ticket, same cause
Decision: the trimmed version of 1a is promoted, 281 words, because the win held and the trimmed version is no worse
```

The user gets a show that does not stop the loop: one rule added, six points removed, 410 → 281 words, 50 of 60 against 44 on the fresh measurement.

In this round the main agent changed nothing, measured nothing and counted nothing. It:

1. chose the criterion and handed out two briefs;
2. saved the cards and sent both candidates to one checker for the round check;
3. received the counts and verdicts: 9 / 1 win, 5 / 3 not proven; 1b broke three tickets, so there was nothing to combine;
4. had the winner filtered, then ordered the deletion from 1a and the re-check;
5. answered the promotion questions and wrote the ledger and `state.md`;
6. sent the user the show and started round 2.

**Rounds 2 to 4.** A normal round and two wide ones. Nobody reached the threshold; the best result was 5 / 2, and no two candidates gained without breaking something, so nothing was combined. A second deletion gave a real break and was discarded. Stop.

**Finish.** On the 30 held-out tickets: 26 against 21 for `v0`, better 6, worse 1. Discordant 7, the table needs 7. Outcome: same direction, not proven. The report says: "On the working panel, 50 of 60 against 44 on the fresh measurement; the win was proven and held. On the held-out tickets, 26 of 30 against 21: the same direction, but 30 examples do not prove it alone."

## 2. Named cases: a database migration plan with no downtime

**Objective.** A plan by which the engineer on call splits the orders table in two with no downtime and no data loss. Criteria by reach:

1. it can be carried out and rolled back: named cases, run on a copy of the database;
2. it is clear to the engineer on call: taste, judged by the user, who is that engineer.

Size budget: 12 steps, the size of `v0`. The user fixed the approach: edit this plan. No wide start.

**Panel** of named cases:

- C1: a run on a copy of production size, under writes;
- C2: rollback after step 3;
- C3: the old application version on the new schema;
- C4: an interruption in the middle of the copy, then a restart.

**Test of the measure.**

- The procedure: how to bring up the copy and run a step.
- A plan without dual writes must fail C1, and it does: 212 rows lost.
- `v0`: C1 and C3 pass. C2 fails: the rollback does not restore the index. C4 fails: the restart duplicates 1,840 rows.
- An attacker gave three findings. One was reproduced: step 5 holds a write lock for 38 seconds. It joined the panel as C5.
- Two were rejected. "Step 2 will block reads": no lock on the copy. "The disk will run out": 14 GB needed with 220 free.

**Round 1.** Failures: C2, C4, C5.

| Candidate | What it added and what that closes | C1 to C5 | Cost |
|---|---|---|---|
| 2a | copying in batches with a progress mark; closes C4 and C5 | C4 and C5 closed, nothing broken | the copy takes 3 h 10 min, not 40 min |
| 2b | the index is built before the switch; closes C2 | C2 closed, nothing broken | none |

Both won, on different cases, with nothing broken. A generator combines them into 2c: all five cases pass, 13 steps. The filter passes: every added step names the case it closes.

An attacker goes at 2c and gives two findings; neither is reproduced.

Deletion from 2c: steps 7 to 9 struck out, manual checks that repeat the automatic one in step 6. 10 steps, down from 13.

Re-check: the check answers the same, so only the trimmed version is measured. All five cases pass. The trimmed 2c is promoted provisionally: it changes what the engineer reads.

**Show 1.** The user gets `best` and `v0` unlabelled, the list of what was added and removed, and the cost. The answer: "The second is clearer. Three hours of copying is fine, it will run at night. Put the check after step 6 back: I will not sign without it."

- By the key the second one is `best`. It becomes `owner`.
- The check after step 6 is restored and the version measured again: 11 steps, all five cases pass. The user's decision is recorded verbatim in `decisions.md`.

**Round 2.** Nothing fails. An attacker goes at `best` and finds nothing that reproduces. Only taste is left, so the loop stops and waits for the user.

There is no statistics here: the cases are named and the check answers the same.

## 3. Taste: the first screen of a landing page

**Objective.** A head of engineering understands from the first screen what is offered and to whom, and scrolls on. Criteria by reach:

| Criterion | Mode |
|---|---|
| the headline and text of the first screen | taste: the user |
| facts | named cases: lookup in the source |
| layout at 390 and 1440 pixels | named cases: rendering |

Size budget: 60 words on the first screen. Shows: after the wide start and after every round.

**Test of the measure.**

- The procedure: render the first screen at two widths, count the words, look up every number on the screen in the source.
- A screen with a three-line headline at 390 pixels is caught by the layout check.
- An attacker on `v0`: "120 projects" where the source says 87. Reproduced.

**Wide start.** Four directions, drawn roughly. Show 1: the four directions and a competitor, unlabelled. The user: "B, but simpler." B becomes `best` and `owner`. The words "but simpler" are the failure for the next round.

**Round 1.** Two candidates and a deletion, all from `best`.

| Candidate | Words | Filter |
|---|---|---|
| 1a | 58 | at 390 pixels the headline runs off the screen; reproduced by rendering, repaired at the first attempt |
| 1b | 59 | passed; a paragraph "for whom" was added, and the card names what it closes: the "to whom" of the objective |
| 1c, deletion | 31 | passed |

**Show 2.** Five versions under letters: `best`, the three candidates and a competitor. The order is shifted by one. The user: "C. A is too complicated. D has a second paragraph it does not need." By the key, C is the deletion, A is 1a, D is 1b.

- 1c becomes `best` and `owner`.
- 1a is discarded with the reason "too complicated".
- 1b is discarded with the reason "a second paragraph it does not need".
- The main agent: "I will hold a check: one paragraph under the headline. Yes?" The user: "No, that was only about D." No check is made.

**Next.** Facts and layout are in order, and there are no new words from the user. The loop stops and waits.

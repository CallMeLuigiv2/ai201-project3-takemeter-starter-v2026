# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

TakeMeter sorts Hacker News comments from AI and LLM threads by what each
comment does:

- `evidence` backs up a point with a link, a quote, a number or a test.
- `opinion` tells you how the writer feels about something.
- `reaction` states, explains, predicts or asks without doing either.

It's `distilbert-base-uncased`, fine-tuned on 284 comments I labelled from 12
of the busiest AI threads of August–October 2026. The aim is to pick the
arguments out of a thread full of takes. In a random sample of these threads,
fewer than one comment in ten backs up what it says.



---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `evidence`

**Definition:** Makes a point and backs it up. The post is built around a
link, a quote, a number or a test result that proves the point. A number
mentioned along the way doesn't make a post evidence.

**Example 1:**
> "Claude Mythos 5.1 is identical to Fable 5.1, but it offers more permissive
> safeguards for vetted individuals and organizations"
>
> Then why does it have separate datapoints for Terminal Bench, and score
> higher? Something doesn't add up here??

**Example 2:**
> "Horizontal integration is the process of a company increasing production
> of goods or services at the same level of the value chain, in the same
> industry. …" [emphasis mine]
>
> https://en.wikipedia.org/wiki/Horizontal_integration

### `opinion`

**Definition:** Tells you how the writer feels about something: a like or a
dislike, or a verdict that it's good, bad, useful, pointless, better or worse.
Sarcasm counts, because it's a feeling aimed at something.

**Example 1:**
> zitron is a jester. He knows his audience and plays it well. It is
> incredibly tiring to listen to his lies, but so are the AI boosters. I think
> it is net zero

**Example 2:**
> Asking AI to review my code is actually pretty useful... but makes the
> process slower.

### `reaction`

**Definition:** States, explains, describes, predicts or asks something
without saying how the writer feels about it and without backing it up. A bold
claim stated flatly is still a reaction.

**Example 1:**
> Like HDR photos of the mid-2000s, these too shall pass.

**Example 2:**
> How does this work if it doesn't change the output?

### The hardest boundary

**Which two labels:** `opinion` and `reaction`

**The decision rule I used every time:** Ask whether the post tells you how
the writer feels about the thing. If it does (a like, a dislike, a verdict
such as "useful", "overpaid" or "terrible", or sarcasm), it's `opinion`, even
when it's about the writer's own use. If the post only states, explains,
describes, predicts or asks, it's `reaction`, even when the statement is bold
("Then realize LLMs have zero of what anyone would consider intelligence").
When a post does both, go with what most of it is doing.

**A second rule, for `evidence` and `reaction`:** a post is `evidence` only
when it's built as "this backs up this". One post gives Hugging Face's
"$150 million in ARR" and then moves on to other statements about the
company. It mentions a number but isn't built on it, so it's `reaction`.


---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:** Hacker News comments from 12 of the busiest AI
threads of August–October 2026, downloaded whole through the HN Algolia API
(13,462 comments). I kept comments of 25–900 characters, in English, with no
code blocks and not just a bare link. The 284 posts in `labels.csv` are:

- 40 from my first card sort
- 180 random comments, 15 from each thread
- 35 random comments that carry a link, a quote or a figure, added because
  `evidence` was thin
- 29 `evidence` posts added for criterion 1, found by screening 90 more
  comments that carry at least two kinds of backing

**How I labelled them:** Claude drafted the first definitions and pre-labelled
the posts. The `note` column says where each label came from:

- `cold`: 20 random posts I labelled myself before seeing Claude's labels.
- `checked` / `corrected from …`: 15 of Claude's labels I went through. I kept
  12 and changed 3.
- `relabelled under the revised rule`: our cold labels matched on only 8 of
  20, so I rewrote the `opinion`/`reaction` rule. Claude then relabelled its
  pre-labels under my rule, including 4 of the ones I had checked.
- `pre-labelled by Claude`: the rest are Claude's labels under my rule.
- `collected to top up evidence`: the 35 and the 29 above. Claude screened
  and labelled the 29.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `opinion` | 119 | 41.9% |
| `reaction` | 108 | 38.0% |
| `evidence` | 57 | 20.1% |
| **Total** | 284 | 100% |

**Three hard cases**

**1.**
> *The post:* "I dunno. You can argue over whether they're overpaying, but it's
> not like Huggingface is Clinkle. They hit $150 million in ARR this year,
> they have tons of runway, and according to reports, have just started to
> even burn the money they raised a few years ago. …" Two more paragraphs
> follow about how open and responsible Hugging Face has been, and what
> Nvidia gets.
>
> *Could have been:* `evidence` or `reaction`
>
> *I chose, because:* `reaction`. It gives the money figures, but then moves
> on to stating other things about the company. The post isn't built as
> "this backs up this", so the figures alone don't make it `evidence`.

**2.**
> *The post:* "Interestingly, it's practically a dead ringer for GLM 5.3's
> version (running https://huggingface.co/incoai/GLM-5.3-NVFP4 locally):
> https://i.imgur.com/QnzlSFp.png"
>
> *Could have been:* `evidence` or `reaction`
>
> *I chose, because:* `reaction`. The two links show what the writer saw. The
> post describes a resemblance; it isn't using the screenshot to prove a
> point someone was arguing about.

**3.**
> *The post:* "Felix, just poking at this, and it is MUCH more pleasant to talk
> to, thanks to your teammates for the work."
>
> *Could have been:* `reaction` or `opinion`
>
> *I chose, because:* `opinion`. Claude first called it `reaction`, a thank-you
> reply to someone on the team. But "MUCH more pleasant" is how the writer
> feels about the model, and the thanks is wrapped around that feeling.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** `distilbert-base-uncased`, fine-tuned on my laptop's CPU
(Intel i3-1115G4, no GPU, torch 2.14.1+cpu). Training took 7 min 44 s for 39
steps.

**Settings:** 3 epochs, learning rate 2e-5, batch size 16, max length 128
tokens, seed 42.

**Anything I changed from the defaults, and why:** Nothing. The only edits to
the notebook were `LABELS` (my three labels) and NAME/EMAIL. One thing to
watch is the 128-token limit. 31 of the 284 posts are longer and get cut off,
and 14 of those are `evidence`, a quarter of that label. Evidence posts often
put their link or source at the end ("[1] https://…"), so the part that makes
them `evidence` can be the part that's cut.

**Split sizes:** 198 train / 43 validation / 43 test, stratified. Seeds 42, 7
and 2024 all give the same per-label counts:

| Label | Train | Val | Test |
|---|---|---|---|
| `opinion` | 83 | 18 | 18 |
| `reaction` | 76 | 16 | 16 |
| `evidence` | 39 | 9 | 9 |

`evidence` has 9 test posts, just over the 8 that criterion 1 asks for. It's
still the thinnest label, and each of its test posts moves its recall by
about 11 points.



---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1: labelling the posts**

- *What I asked for:* I asked Claude to label the posts and give me a page
  with a checkbox for each label, so I could agree or disagree with each one.
- *What came back:* a three-label draft (`evidence`, `opinion`, `reaction`;
  Claude dropped a `question` label because only 11 of 220 posts were real
  questions), pre-labels for the posts, and a review page. The page hid
  Claude's labels on 20 random posts so I could label those cold.
- *What I changed:* My cold labels matched Claude's on only 8 of 20 (40%),
  and 10 of the 12 misses were `opinion` against `reaction`. Claude had drawn
  the line at "does it make a claim about the world?" I replaced that with my
  own rule: `opinion` is how the writer feels about something, and `reaction`
  is stating or detailing something. I also tightened `evidence` to posts
  built as "this backs up this". Claude relabelled its pre-labels under my
  rule, and 108 of 220 changed.

**Moment 2: the acceptance criteria**

- *What I asked for:* what the five criteria could be about, with the reasons
  for each.
- *What came back:* seven measurable areas, each with a question to answer.
  These were overall accuracy, `evidence` F1, `opinion` and `reaction` F1,
  macro F1, label balance, consistency and confidence. Each came with what it
  would mean for my 39-post test split.
- *What I changed:* I picked five of them (label balance, `evidence` F1,
  macro F1, `opinion` and `reaction` F1, confidence) because of how my data
  is spread: `evidence` was 11% of the labels, and `opinion` against
  `reaction` was my hardest boundary. **I asked Claude to set the numbers**,
  and it did. It set each target above what guessing or always answering
  `opinion` would score, and below what labels this uncertain could support.
  Criterion 1 then meant collecting more `evidence` posts before training.

**Pre-labelling disclosure:**

- Claude drafted the first label definitions and pre-labelled every post
  except the 20 cold ones.
- I labelled 20 random posts cold, without seeing Claude's labels
  (`cold` in the note column).
- I went through 15 of Claude's labels on the review page. I agreed with 12
  and changed 3 (`checked` / `corrected from …`). Four of the 12 were later
  relabelled under my revised rule (`relabelled under the revised rule`).
- The remaining 249 labels are Claude's, applied under my rule
  (`pre-labelled by Claude`). They include the 64 posts collected to top up
  `evidence`. For the last 29 of those, Claude screened 90 comments and kept
  the ones it labelled `evidence`.
- Claude also wrote the scripts that downloaded and sampled the threads and
  built `labels.csv`. It ran the notebook and drafted this README and
  `NOTES.md` from my decisions.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

Both models are scored on the same 43 held-out posts (`test_split.csv`,
seed 42). The baseline is `facebook/bart-large-mnli`, zero-shot, given my
definitions from `label_definitions.txt`. The trained column is
`results.json` from unit 5.

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.326 | 0.535 | +0.209 |
| Macro F1 | 0.251 | 0.519 | +0.268 |
| F1 — `evidence` | 0.286 | 0.750 | +0.464 |
| F1 — `opinion` | 0.000 | 0.625 | +0.625 |
| F1 — `reaction` | 0.468 | 0.182 | -0.286 |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

The trained model beats the baseline on all three labels, because it learned
from 198 of my own labelled posts and the baseline learned from none.

(I chose this from four predictions Claude drafted out of what I already knew:
the baseline numbers, my training-set size, and how hard the
`opinion`/`reaction` line was to label. I hadn't seen the trained numbers.)

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->

Fine-tuning added a lot overall: +0.21 accuracy and +0.27 macro F1. Nearly
all of it comes from two labels, `opinion` (0.00 to 0.63) and `evidence`
(0.29 to 0.75).

My prediction was wrong on `reaction`. The trained model scores 0.18 there,
worse than a model that never saw my data. It called 13 of the 16 real
`reaction` posts `opinion`, and it only answered `reaction` 6 times in 43.

The baseline's 0.47 on `reaction` isn't skill either. It answered `reaction`
for 31 of the 43 posts and never answered `opinion`, so it catches
`reaction` posts by guessing that label almost every time. Its `reaction`
precision is 0.35.

So neither model separates `opinion` from `reaction`. Each one pushes the
pair onto one side: the baseline onto `reaction`, the trained model onto
`opinion`. What fine-tuning bought is `evidence`, and the line between "backs
it up" and "doesn't". It didn't buy my hardest boundary.

**Two side results:**

- **Bare label names beat my definitions.** Run with only the words
  `evidence`, `opinion` and `reaction`, the baseline scores 0.402 macro F1
  instead of 0.251 (`baseline_results_bare_names.json`). `baseline.py` puts
  each definition into the sentence "This post is {}.", and mine start with a
  verb, so they read "This post is tells you how the writer feels…". My
  `reaction` definition ("states, explains, describes, predicts or asks
  something") also fits almost any post. The table above uses the
  definitions run, which is the course default. The trained model still
  beats the stronger bare-names baseline (0.519 against 0.402).
- **Speed.** The baseline took 59 seconds for the 43 posts, model loading
  included, on my desktop's CPU (Ryzen 9 5900X). The trained model scores the
  same 43 in about a second.



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

Same notebook and settings as unit 5, run three times with seeds 42, 7 and
2024 (`results_three_seeds_before.json`). Each seed gives a different
198 / 43 / 43 split with the same per-label counts, so every test split has
9 `evidence`, 18 `opinion` and 16 `reaction`.

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Label balance | `evidence` ≥ 18% of the file and ≥ 8 in test; no label > 45% | 20.1%, 9, 41.9% | 20.1%, 9, 41.9% | 20.1%, 9, 41.9% | MET |
| 2. `evidence` F1 | ≥ 0.50 | 0.750 | 0.500 | 0.667 | MET |
| 3. Macro F1 | ≥ 0.60 | 0.519 | 0.412 | 0.523 | MISSED |
| 4. `opinion` and `reaction` F1 | each ≥ 0.60 | 0.625 / 0.182 | 0.642 / 0.095 | 0.680 / 0.222 | MISSED |
| 5. Confidence gap (most-sure third minus least-sure third) | ≥ 0.20 | 0.214 | 0.071 | 0.214 | MISSED |

The spread table from the notebook's section 6:

| Measure | Seed 42 | Seed 7 | Seed 2024 | Spread |
|---|---|---|---|---|
| Overall accuracy | 0.535 | 0.488 | 0.581 | 0.093 |
| Macro F1 | 0.519 | 0.412 | 0.523 | 0.111 |
| F1 — `evidence` | 0.750 | 0.500 | 0.667 | 0.250 |
| F1 — `opinion` | 0.625 | 0.642 | 0.680 | 0.055 |
| F1 — `reaction` | 0.182 | 0.095 | 0.222 | 0.127 |

Accuracy moves 9 points between seeds and macro F1 11. Criterion 2 is MET by
the smallest margin possible: seed 7 lands exactly on 0.50, and one more
missed `evidence` post would have made it a miss. `opinion` is the steadiest
number in the table. `reaction` fails on every seed.

**Hardware.** These three runs are on my desktop's CPU (AMD Ryzen 9 5900X,
torch 2.14.1+cpu), about 65 seconds per seed. Unit 5's run was on my laptop's
CPU (Intel i3-1115G4), 7 min 44 s. Seed 42 came out identical on both machines
in every score and in the confusion matrix. The only difference is in the 8th
decimal place of one average confidence (0.47051844 against 0.47051841),
which is the different-hardware arithmetic `RUNNING.md` warns about.

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

Seed 42, as the notebook's section 7 prints it:

| true \ predicted | evidence | opinion | reaction |
|---|---|---|---|
| **evidence** | 6 | 2 | 1 |
| **opinion** | 0 | 15 | 3 |
| **reaction** | 1 | 13 | 2 |

All three seeds added together (129 test posts):

| true \ predicted | evidence | opinion | reaction |
|---|---|---|---|
| **evidence** | 15 | 8 | 4 |
| **opinion** | 1 | 49 | 4 |
| **reaction** | 3 | 40 | 5 |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->

13 real `reaction` posts were called `opinion`, and only 3 `opinion` posts
went the other way. Across all three seeds it's 40 of 48 `reaction` posts
called `opinion`, against 4 the other way. The model hasn't learned my
`opinion`/`reaction` line at all. When a post doesn't back anything up, it
answers `opinion`, which it did for 97 of the 129 test posts. The
`evidence` line it did learn: only 4 of the 102 non-`evidence` posts were
called `evidence`.



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Label balance | `evidence` ≥ 18% of the file and ≥ 8 in test; no label > 45% | MET | `evidence` is 20.1% with 9 test posts on every seed; the largest label is 41.9%. |
| 2 | `evidence` F1 | ≥ 0.50 on all three seeds | MET | 0.750, 0.500, 0.667. Seed 7 lands exactly on the line, so this passes with no margin. One more missed `evidence` post would make it a miss. |
| 3 | Macro F1 | ≥ 0.60 on all three seeds | MISSED | 0.519, 0.412, 0.523. Below on every seed. |
| 4 | `opinion` and `reaction` F1 | each ≥ 0.60 on all three seeds | MISSED | `opinion` passes on every seed (0.625, 0.642, 0.680). `reaction` fails on every seed (0.182, 0.095, 0.222). |
| 5 | Confidence gap | ≥ 0.20 on all three seeds | MISSED | 0.214, 0.071, 0.214. Seed 7 misses. |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->

**Criteria 3 and 4 miss for one reason: `reaction`.** The model never
learned my `opinion`/`reaction` line. Across the three seeds it called 40 of
the 48 `reaction` test posts `opinion`. Going through the four causes:

- **Too few examples?** No. `reaction` has 76 training posts and `opinion`
  has 83. `evidence` has only 39 and scores far better.
- **A boundary I applied inconsistently?** The agreement report says not by
  me. Staff's `reaction`/`hot_take` line is the same kind of line as my
  `opinion`/`reaction` one: a feeling against a statement. Of the 20 staff
  posts on either side of it, I crossed it once, and that was a misread
  (disagreement #4). If I applied a feeling-against-statement rule loosely,
  it should show up on 30 posts, and it doesn't. The limit is that 249 of my
  284 labels are Claude's, applied under my rule, and this check doesn't
  measure those. The only direct test of Claude against me came before I
  rewrote the rule (40% on 20 cold posts). After the rewrite I reviewed 15
  of its labels and kept 12. So noise in the pre-labels isn't ruled out.
- **A genuinely hard pair?** Partly. It's the hardest line in my taxonomy:
  10 of the 12 cold-test disagreements were on it. But a hard pair gets
  confused in both directions, and here it's 40 one way against 4 the other.
  That's a model that has picked a side, not one that's torn.
- **A task the model can't reach from this much training?** This fits
  best. The training loss averaged 0.98–1.00 over the 3 epochs, against
  1.10 for guessing evenly. Validation loss was still falling at epoch 3 on
  all three seeds (seed 42: 1.02, 0.94, 0.92). The model stopped after 39
  steps, having learned "a link or a number means `evidence`" and little
  else. When it's unsure it falls back on `opinion`, the biggest label (42%
  of the file).

**Criterion 5 misses for the same reason.** The model is barely more
confident when it's right than when it's wrong: an average of 0.471 against
0.456 on seed 42, 0.427 against 0.417 on seed 7, and 0.436 against 0.427 on
seed 2024. With confidence that flat, splitting the test posts into a sure
third and an unsure third is close to noise. Two seeds cleared 0.20 by
3 posts out of 14. Seed 7 managed 1.

**What this points at.** The agreement report doesn't point at my
labelling, and the training logs point at a model that stopped too early.
So the fix to try first is on the model side, which is Milestone 5.



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** 26 / 30 = 87%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

I labelled the 30 posts cold under `data/staff_taxonomy.md` and committed
`my_staff_labels.csv` before I asked for the staff labels. Staff marked 7 of
the 30 as borderline, and I matched them on 6 of those 7.

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

**1.**
> *The post:* "Everyone blames the hill at 18 miles. I split the field into
> people who ran the first half under 1:45 and over, and the slowdown after 18
> was almost identical for both groups. If the hill were the problem the fast
> starters would fall apart harder."
>
> *Staff said / I said:* `analysis` / `hot_take`
>
> *My call, and why:* Staff is right. Rule 1 asks whether the checkable fact
> carries the argument, and here it does. The writer splits the field at a
> 1:45 first half, which is a named comparison, and the whole conclusion
> (it isn't the hill) rests on the two groups slowing down the same. I read
> the confident opening, "Everyone blames the hill", as a take and stopped
> there.
>
> *Which pile:* (a), the rule covered it and I applied it loosely.

**2.**
> *The post:* "Every injury thread on this forum is the same injury and the
> same three replies."
>
> *Staff said / I said:* `hot_take` / `analysis`
>
> *My call, and why:* I'd keep `analysis`. The definition asks for a
> specific, checkable fact, and this post is one: a claim about the forum's
> own threads that anyone could count. It isn't a feeling and it isn't a
> verdict on the sport. It reports a pattern. Staff's own note says it "could
> be checked in principle", and their objection is that no count is given.
> The definition says the fact has to be checkable. It doesn't say the writer
> has to show the count. The weak point in my reading is that the post
> doesn't argue from the fact to anything else; the observation is the whole
> post. Staff flagged it as borderline too.
>
> *Which pile:* (c), the rule is ambiguous here and my reading is defensible.

**3.**
> *The post:* "First run back after eight weeks out. Two miles, slower than my
> warm-up used to be, and I don't care even slightly."
>
> *Staff said / I said:* `reaction` / `analysis`
>
> *My call, and why:* Staff, narrowly. It's a tweener. "Slower than my
> warm-up used to be" is a checkable comparison, and rule 3 says recency
> doesn't outrank evidence, which is why I went to `analysis`. But the
> comparison doesn't argue anything. It describes the run, and the post ends
> on how the writer feels ("I don't care even slightly"). The `reaction`
> definition (just happened, about the writer, a feeling) fits all of it.
>
> *Which pile:* (b), the rules don't say. Rule 1 covers a fact that carries an
> argument, and rule 3 says recency doesn't beat evidence. Neither says what
> to do with numbers that only describe the writer's own experience.

**4.**
> *The post:* "Signed up. Card's charged, no going back, immediately regret
> everything."
>
> *Staff said / I said:* `reaction` / `hot_take`
>
> *My call, and why:* Staff is right; I misread it. Rule 2 asks whether the
> post is about a claim or about the writer. This one is all about the
> writer, in the moment: signed up, card charged, regret. "Regret everything"
> is a feeling, not a general claim about the world.
>
> *Which pile:* (a), the rule covered it and I applied it loosely.

**What the pattern in my disagreements tells me:**

- **Three of the four turn on rule 1: does the fact carry the argument?** I
  went both ways on it, too strict once (#1) and too loose twice (#2, #3).
  In my own taxonomy that's the `evidence` line ("this backs up this"), so
  my `evidence` labels probably carry the same wobble. It's also the line my
  model handles best (`evidence` F1 0.50–0.75), so the wobble didn't stop it
  learning that label.
- **Only one crossed the feeling against general-claim line** (#4), and it was
  a misread. Staff labelled 20 of the posts `reaction` or `hot_take`, and I
  crossed between those two labels once. That's the staff version of my own
  `opinion`/`reaction` line, a feeling against a statement, and it's exactly
  where my model fails (40 of 48 `reaction` posts called `opinion`).
- So on these 30, I applied a written feeling-against-claim rule
  consistently. That points the model's worst confusion away from me
  applying my rule loosely. The limit is that this check measures *my*
  labelling. 249 of my 284 labels were applied by Claude under my rule, and
  this check says nothing about those.



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**

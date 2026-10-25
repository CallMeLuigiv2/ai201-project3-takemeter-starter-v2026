# Working notes

Notes kept while building TakeMeter, one section per milestone. The README is
the write-up; this file is the trail behind it.

## Milestone 1 — community and candidate distinctions

**Community:** Hacker News (news.ycombinator.com), the comment sections of
AI and LLM stories. It's public with no login, it has a lot of text, and the
quality varies a lot within a single thread. The posts come from 12 of the
busiest AI threads of August–October 2026: model launches (GPT-6 Astra,
GPT 6.1 Sol, Claude Opus 5.5, Claude Fable 5.1), Nvidia buying Hugging Face,
AI in mathematics, Google's AI answers, Ed Zitron's predictions, and an
agent message board OpenAI discovered. Many threads on many topics, so no
single release dominates, and nothing in a post needs a spoiler warning.

**First attempt:** my first sort grouped comments by topic: open-weight
models, AI and maths proofs, AI creativity, software optimisation. Topic piles
can't be labels. They say what a comment is about, not what it does, and
every thread would get its own pile.

**Candidate distinctions**, each one something I can point at in a post:

1. **Some comments give you something to check. Most don't.** A number, a
   price, a benchmark score, a link, a quote from the article.
   > They hit $150 million in ARR this year, they have tons of runway, and
   > according to reports, have just started to even burn the money they
   > raised a few years ago.

   against
   > Nvidia overpaid for Hugging Face.

2. **Some comments make a claim about the world. Others only respond.**
   Thanks, a joke, a story about what happened to the writer. No claim about
   a company, a model or the future.
   > I was trying to find a song which contained something like "not getting
   > rained on" and google AI just congratulated me that I avoided rain.

   against
   > Academia has been a toxic wasteland for decades.

3. **Some questions want an answer. Many are claims in question form.**
   > Tabling as they do in the US, or tabling as they do in Britain?

   against
   > Sure but why? Outside of a small joy of the organizing committee, is an
   > incrementally "better" (which is almost entirely subjective anyway)
   > worth the trouvle?

4. **Replies argue with one person. Top-level comments answer the story.**
   "You may be living in a bubble." is aimed at a commenter. "Nvidia overpaid
   for Hugging Face." is aimed at the news. I noticed this one but it's about
   where a comment sits in the thread, which the thread already records, so
   it isn't a label.

## Milestone 2 — labels

Three labels, `evidence`, `opinion` and `reaction`. Definitions, examples and
the decision rules are in the README under Label Taxonomy.

**A label that didn't hold: `question`.** The first draft had a fourth label
for questions. Only 11 of the first 220 posts were real questions, which would
leave about 2 in the test split. Telling a real question from a rhetorical one
was also the least consistent call. "Sure but why? … is an incrementally
'better' … worth the trouble?" is a claim that it isn't worth it, dressed as a
question. Real questions now go in `reaction`. A rhetorical question carrying
a verdict is `opinion`.

**Stress test.** Claude drafted the first definitions and pre-labelled the
posts. I labelled 20 random posts cold without seeing its labels, then we
compared. We matched on 8 of 20 (40%; guessing gets about 33%). Ten of the
twelve disagreements were `opinion` against `reaction`:

- 7 times Claude said `opinion` and I said `reaction`, on flat statements
  like "Like HDR photos of the mid-2000s, these too shall pass." and "It is
  using the search engine. The AI summary is run over the top 100 results…"
- 3 times Claude said `reaction` and I said `opinion`, on first-person
  verdicts like "Asking AI to review my code is actually pretty useful… but
  makes the process slower."
- The other 2: Claude called a link-and-screenshot comparison and a worked
  calculation `evidence`. I called both `reaction`.

The draft drew the line at "does it make a claim about the world?" That isn't
how I was reading posts, so I rewrote the rule: `opinion` is how the writer
feels about something, and `reaction` is stating or detailing something. I
tightened `evidence` too. It has to be a "this backs up this" post, so a post
that mentions Hugging Face's $150M ARR and moves on doesn't count.

**Borderline post:** "Asking AI to review my code is actually pretty useful…
but makes the process slower." It's the writer's own experience, which pulls
toward `reaction`, but "pretty useful" and "slower" are a verdict. Under the
rule it's `opinion`.

Claude then relabelled its other pre-labels under the new rule, and 108 of
220 changed. Counts now: `opinion` 119 (47%), `reaction` 108 (42%),
`evidence` 28 (11%).

## Milestone 4 — the dataset

`labels.csv` has 284 rows: `opinion` 119 (41.9%), `reaction` 108 (38.0%),
`evidence` 57 (20.1%). Where they came from and how they were labelled is in
the README under The Dataset.

**Topping up `evidence` for criterion 1.** After the relabel, `evidence` was
28 of 255 (11%), with 4 posts in the test split. The 35 posts first collected
for their links, quotes and figures only yielded 8 `evidence`, because most
of them mention a number or link and move on. So the second pass looked for
comments carrying at least two kinds of backing (a link, a quoted line, a
figure, a test, a named source), spread evenly across the 12 threads. Claude
read 90 of them under the revised rule and kept the 30 that are built as
"this backs up this". Six that could go either way were left out, for
example "Has the government stopped Google and Apple?" with a link. Is that a
rhetorical question or proof?

**One duplicate.** One kept post was the same OpenAI quote and link as a
post already in the sample, from the same thread, minus the word "Sure." It
would have leaked across splits, so it was dropped, which left 29.

**Checks.** The notebook's own data check reports no problems and no
warnings. There are no exact or near-duplicate texts. All three seeds (42, 7,
2024) split 198 / 43 / 43, with a test split of 18 `opinion`, 16 `reaction`
and 9 `evidence`. That puts criterion 1 at `evidence` 20.1% (target 18%), 9
test posts (target 8) and largest label 41.9% (cap 45%).

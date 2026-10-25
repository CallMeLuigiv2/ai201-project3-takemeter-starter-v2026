# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1. Label balance

`evidence` makes up at least **18%** of `labels.csv`, which puts at least
**8** `evidence` posts in the test split on every seed. No label is more than
**45%** of the file.

**Why this target:** The first 255 posts had only 28 `evidence` (11%), and
that put just 4 in the test split. With 4 posts, one missed post moves
`evidence` recall by 25 points, and the notebook warns when a label has fewer
than 8 test posts. At this dataset size, 18% is about where the split reaches
8. `opinion` is the biggest label at 47%. Holding every label to 45% means a
model that answers `opinion` every time gets well under half the posts right.

---

## 2. `evidence` F1

`evidence` has an F1 of at least **0.50** on the test split, on all three
seeds.

**Why this target:** `evidence` is the thinnest label. Even after the top-up it
has fewer than 40 training posts, so its bar sits below the other two. With
8 test posts, 0.50 means finding about half of them without as many false
calls as hits. Guessing in proportion to the label shares scores about 0.2.
These posts are built around a link, a quote or a number that's visible in
the text, so beating chance by a wide margin is a fair bet.

---

## 3. Macro F1

Macro F1 is at least **0.60** on the test split, on all three seeds.

**Why this target:** Macro F1 weights `evidence` the same as the two big
labels, so a model can't pass by ignoring it the way it could with accuracy.
Random guessing scores about 0.33, and answering `opinion` every time scores
about 0.20. Criteria 2 and 4 together only guarantee
(0.50 + 0.60 + 0.60) ÷ 3 = 0.57. So 0.60 asks for more: if `evidence` lands
at 0.50, `opinion` and `reaction` have to average at least 0.65.

---

## 4. `opinion` and `reaction` F1

`opinion` and `reaction` each have an F1 of at least **0.60** on the test
split, on all three seeds.

**Why this target:** These two labels are about four-fifths of the data, and
the line between them is the hardest one in the taxonomy. In the cold test,
10 of the 12 disagreements were `opinion` against `reaction`. So the labels
near that line are the least certain in the set, and a bar of 0.80 or more
would ask for more consistency than the labels have. Random guessing gets
about 0.35–0.42 on each. Answering `opinion` every time already gets about
0.60 on `opinion` alone, which is why `reaction` has to clear the same bar.

---

## 5. Confidence

On every seed, accuracy on the model's most-confident third of the test split
is at least **0.20** higher than on its least-confident third. That's about 3
more posts right out of 14.

**Why this target:** Thin `evidence` and a fuzzy `opinion`/`reaction` line
mean a good share of the test posts are borderline. If the model's confidence
means anything, those posts should land in its unsure third, and its sure
third should be clearly better. One post is worth about 0.07 here, so a gap
of 1 or 2 posts could be chance. 3 is the smallest gap that says the
confidence is real.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->

---

layout: post
title: When Better Reasoning Traces Hurt
date: 2026-02-27 10:14:00-0400
description: On the failure of SFT data at small scale and what student learnable quality actually looks like
tags: AI
categories:
giscus_comments: false
featured: true
related_posts: false

toc:
  sidebar: left
---

tl;dr I spent some time trying to make a small language model better at math by being more careful about its training data. The model got worse. I made the data more careful. The model got worse. I tried to fix the fix. It got worse a third time. This is a write up of why and of a result I genuinely did not see coming: the same intervention that hurts a 0.5B model helps a 3B model, and helps a 7B model more.

---

There was this classic LLM failure mode that was trending recently on twitter. When asked the question

*I want to wash my car. The car wash is 50 metres away. Should I walk or drive?*

many SOTA models told the user to walk. Here is what the latest version of ChatGPT gave me when I tried the query.

{% include figure.liquid path="assets/img/posts/trace_quality/gpt_answer.png" class="img-fluid rounded z-depth-1 mx-auto d-block" width="100%" max-width="760px" height="auto" zoomable=true %}

It had completely ignored the first sentence, the part where I said I wanted to wash the car. If you are washing the car, the car has to come with you, so walking defeats the whole point. Examples like this are why there is a recurring debate about whether these models reason at all.

I can't fully explain this particular failure mode. My best guess is that the problem is not that the model can't reason, but that it doesn't have a good model of the thing it is reasoning about, in this case how the physical situation actually works. The phrasing looks almost identical to the thousands of "it's only 50 metres, should I walk or drive?" examples it has seen, where walking is the sensible, healthy, eco-friendly answer. And this strong prior overrides the constraint that the car here is not just a vehicle of transportation, but a cargo that needs to be shipped. To me this is a clean instance of language alone being insufficient to ground a model in how the world works.

I was curious about how to fix these kind of failure modes. The obvious move is to finetune the model on similar examples paired with reasoning traces that surface the constraint explicitly. That got me thinking about a more targeted, data quality angle.

Data curation for SFT, the stage where models learn to imitate reasoning, mostly consists of generating synthetic reasoning traces from a bigger model and filtering them for correctness. People rarely question the quality of a trace beyond "did it reach the right answer." But are some reasoning traces genuinely better teachers than others? And can we use feedback from a model's failures to improve the traces themselves, and through them the model's performance? That is the question this post is about. The answer turned out to be more interesting than a plain yes or no.

Related work like STaR and SuperCorrect already uses feedback from a model's failures, but in a different way than what I am after.

STaR (Self-Taught Reasoner) runs a bootstrapping loop. You few shot prompt the model to produce a rationale and an answer for each problem, keep every rationale that lands on the correct answer, and finetune on those. For the problems it gets wrong, STaR feeds the model the correct answer as a hint and asks it to write a fresh rationale that arrives there, a step the authors call rationalization. Those rationalized traces get folded into the training set, and the loop repeats. Failure here is essentially a filter, plus a way to backfill rationales for the harder problems. 

SuperCorrect goes a step further and brings in a teacher in a two stage setup. In the first stage, reasoning traces are distilled from a large teacher model to supervise a smaller student. In the second stage, the teacher inspects the student's faulty reasoning, locates the specific step that went wrong, and produces a correction trace. Those traces become preference pairs that teach the student to find and fix its own errors rather than just imitate a correct path. So failure feedback is localized and corrective here, but the goal is still to sharpen the student's self correction, not to ask which traces make the best teaching data in the first place.

That last gap is where this post lives.

---

### The setup

The pipeline involves using a stronger model (Qwen2.5-72B) to solve GSM8K problems, filter the correct ones whose reasoning traces conform to specific defined criteria judged by another stronger model and then finetuning a smaller model on the resulting traces. The smaller model is a base model and we use standard finetuning hyperparameters since this is just a validation run. 

Same problems, same teacher, same hyperparameters across runs. The only thing that varies is the *prompt* used to ask the teacher how to write it's reasoning. That isolates the reasoning trace as the variable.

I designed four prompts, and the four datasets they produced will be discussed through the rest of this post:

- **Normal** : "solve the problem step by step." Nothing else.
- **Gold** : explicitly addresses the failure modes I'd catalogued in the base model. Restate the problem. Translate tricky wording. Restate intermediate values when reusing them. Check at the end that you answered what was asked. Numbered structure.
- **Titanium** : a strict schema, the kind of thing a programmer would design. Goal on top, every fact labelled `F1, F2…`, every translation labelled `X1, X2…`, every calculation labelled `C1, C2…`, answer at the bottom. No prose at all.
- **Diamond** : careful attention to failure points, but as natural prose. No labels, no headers, no schema. Inline arithmetic.

Here's the reasoning trace for the same problem produced by the four prompts:

> **Problem:** Julie is reading a 120-page book. Yesterday she read 12 pages, and today she read twice as many pages as yesterday. If she wants to read half of the remaining pages tomorrow, how many pages should she read tomorrow?

**Normal:**
> Yesterday, Julie read 12 pages. Today, she read twice as many pages as yesterday, so she read 2 × 12 = 24 pages. The total number of pages she has read so far is 12 + 24 = 36 pages. The number of pages remaining in the book is 120 − 36 = 84 pages. Julie wants to read half of the remaining pages tomorrow, so she should read 84 / 2 = 42 pages. #### 42

**Gold:**
> Julie is reading a book that has 120 pages. Yesterday, she read 12 pages, and today she read twice as many pages as she read yesterday. We need to find out how many pages she should read tomorrow.
>
> Step 1: Calculate the number of pages Julie read today.
> Pages read today = 2 × 12 = 24
>
> *[... continues for four numbered steps ...]*
>
> The question asked for the number of pages Julie should read tomorrow. My answer is 42.
>
> #### 42

**Titanium:**
> G: Number of pages Julie should read tomorrow
> F1: Julie is reading a 120-page book
> F2: Yesterday, she read 12 pages
> F3: Today, she read twice as many pages as yesterday
> C1: pages_read_today = 2 × 12 = 24
> C2: total_pages_read = 12 + 24 = 36
> C3: remaining_pages = 120 − 36 = 84
> C4: half_remaining_pages = 84 / 2 = 42
> ANSWER: 42
>
> #### 42

**Diamond:**
> Yesterday, Julie read 12 pages. Today, she read twice as many pages as yesterday, which is 2 × 12 = 24 pages. So far, she has read 12 + 24 = 36 pages. The book has 120 pages, so the remaining pages are 120 − 36 = 84 pages. If she wants to read half of the remaining pages tomorrow, she should read 84 / 2 = 42 pages. #### 42

Gold is carefully written: it rephrases the question, works through the calculation in steps, and refocuses on what is being asked before committing to a final answer. Titanium is the most rigorous in a formal systems sense, it assigns a variable name to every quantity, which should make it hard to silently drop a term. Normal is the laziest of the four. If you had shown me these four traces cold and asked which would teach a small student model best, I would have ranked them roughly as Titanium and Gold at the top, the two of them competing for first, then Normal at the bottom. The results did not agree.

---

### The result, in one chart

{% include figure.liquid path="assets/img/posts/trace_quality/fig_scale.png" title="Main result: trace style vs student size" class="img-fluid rounded z-depth-1 mx-auto d-block" width="100%" max-width="760px" height="auto" zoomable=true %}

At 0.5B, the laziest dataset wins. Every careful, well motivated intervention I designed made things worse. Titanium, the dataset I was most confident about, hit exactly the un-finetuned base model's accuracy.

At 3B, the ranking flips at the top. Gold overtakes Normal by 2.4 points. Diamond and Titanium are still below, and at 7B we see a similar picture.But before I get to what I think it means, let me walk through the experiments in order.

---

### Let there be Gold

I started by reading every wrong answer the 0.5B base model produced on 50 GSM8K problems. The errors weren't random, they clustered into patterns that recurred over and over: 
- comparative wording inverted, 
- premises forgotten by step three, 
- intermediate values dropped, 
- scopes confused (treating a per day rate as a total) etc.

These felt like teachable mistakes, the kind a careful tutor would address. So I built Gold around them. Restate the problem at the top, to force engagement with what's actually being asked. Translate tricky comparative wording before using it, to make relation direction explicit. Restate values when reusing them, so the trace never relies on the model holding state across many tokens. Completion check at the end. I asked the 72B teacher to solve about 2000 GSM8K problems in this style, filtered for correctness and traces that followed the prompt instructions, and finetuned the 0.5B on the result.

Training looked beautiful. Gold's training loss was lower than Normal's but the eval told a different story:

<table class="table table-sm table-bordered">
  <thead>
    <tr>
      <th>Dataset</th>
      <th>0.5B accuracy</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Normal</td>
      <td><strong>49.7%</strong></td>
    </tr>
    <tr>
      <td>Gold</td>
      <td>47.3%</td>
    </tr>
  </tbody>
</table>

Gold was worse. The dataset I'd designed specifically to address the model's failure modes performed worse than the one I'd generated with no instructions at all. This was a bit suprising and disappointing but after looking at about 30 outputs of the model it wasn't hard to see that Gold's structure (numbered headers, restatement preamble) seemed to be competing with it's reasoning ability. The structure is different from what the model had seen during pretraining hence the model was spending some of its limited capacity learning Gold's **format** instead of Gold's **math**.

---

### Doubling down: And then there was Titanium

The fix, I thought, was to commit harder. If the model was struggling with Gold's prose-around-structure, give it a cleaner structure. Make every part of the trace mechanical, unambiguous, named. Take away the prose entirely.

That gave me Titanium. Every fact on its own line with an `F1` label. Every translation with `X1`. Every calculation with `C1`. Goal at the top, answer at the bottom. The model literally cannot drop a constraint, because each one has its own row with its own identifier. There is no ambiguity for it to misread. 

Titanium scored **33.3%**.

That's the base model. To one decimal place. A full finetuning run on a clean, schematic, perfectly labelled dataset accomplished nothing measurable. The model learned to predict `F2:` after `F1:` and went home. We'll discuss more about the failures of Titanium after looking at Diamond dataset but at this point it was obvious that the structure was hurting the model's ability to learn at least at this scale.

---

### Be Fruitful: Diamond

So Gold failed because its structure pushed traces off distribution. Titanium failed because its schema turned reasoning into a fill in form. What I needed, clearly, was Normal's prose distribution with Gold's pedagogical care, and absolutely no schema to imitate.

I called that Diamond. The teacher prompt was roughly: "Write naturally, with inline arithmetic. Be explicit at exactly the decision points where small models fail — ratio direction, comparative wording, state tracking, scope. No bullets. No numbered steps. No labels. Just careful prose."

This had to work. It was essentially Normal with the dial of careful attention turned up. No format to imitate, no schema cost, just better English at the points where the model was known to slip.

Diamond scored **45.7%**.

Below Normal. Again.

---

### The fourth day 

Three carefully designed datasets in a row, and the version with no instructions at all was still beating them.

At this point I had to seriously consider that I had been wrong about something more fundamental than which formatting style to pick.

Let's Look at the training loss for the four runs:

{% include figure.liquid path="assets/img/posts/trace_quality/fig_loss.png" title="Training loss is misleading" class="img-fluid rounded z-depth-1 mx-auto d-block" width="100%" max-width="760px" height="auto" zoomable=true %}

Starting with the panel on the left, the train loss, two things are easy to read off it. First, Normal and Gold sit at the top of the band, plateauing around 0.09–0.10. Second, Titanium and Diamond sit lower, around 0.05–0.07. Titanium ends up just under Diamond, but on the train side the gap between them is small.

The fact that Titanium reaches very low train loss is unsurprising once you've stared at a Titanium trace. The schema (F1:, C2:, ANSWER:, the structural punctuation) gives the model a huge amount of token level predictability. Once it's seen F1: it knows F2: is coming. The schema is, mechanically, just easier to fit. The fact that Diamond is sitting down there with Titanium is the part that surprised me. Diamond has no schema at all. So why is Diamond down with Titanium on train loss instead of up with Normal and Gold? My guess is that Diamond's prompt creates a different kind of predictability which is not structural, but stylistic. The teacher was instructed to write in a tight, compressed, inline-arithmetic register, and across 1,700 traces that register is consistent. Normal and Gold, by contrast, were generated with much looser prompts: across their traces, the same idea ("she has read a total of") might appear as "altogether, she's read," "the total is so far," "in total she's read," or any of a dozen other phrasings. Diamond's prose is more uniform across examples, so once the model picks up its rhythms, predicting the next token within that style gets cheap.

Now look at the eval panel on the right. The Titanium-Diamond gap, which was small on training, opens up clearly on held out data. This second observation is the one that pinned down what was actually going on for me. If both Titanium and Diamond were just "narrow distributions are easy to fit," they should track each other on eval too but they don't. Titanium's eval loss advantage over Diamond is bigger than its train loss advantage. Something is letting the model predict held out Titanium examples more efficiently than it predicts held out Diamond examples. That something is the schema's positional predictability. Stylistic narrowness (Diamond) is generic, once you've learned the register, you have to roughly guess what content comes next at every position. Schematic narrowness (Titanium) is much stronger, once you've learned the template, you know not just that the next token will be in a particular style, but that it sits at a known slot: after C2: comes an arithmetic expression of a known shape; after ANSWER: comes a single number followed by a newline. The schema acts like positional anchors, which generalises perfectly to any other Titanium formatted example regardless of the specific math. Diamond's prose register doesn't give you anchors of that kind. You still have to predict the next word in the flow.

Both of these make loss go down. Neither of them has anything to do with the model getting better at math. This was the first big update for me, and it's the kind that feels obvious in retrospect: low SFT loss does not always mean good reasoning supervision. It could also mean the dataset is more compressible, either stylistically, or schematically, or both. The math content is approximately constant across all four of these datasets. What varies is the prose around it. And the prose is what loss is dominated by, because it's most of the tokens.

I had been judging traces by how clear and sound they looked to me. Like a teacher writing for a textbook. But a 0.5B model doesn't read. It predicts tokens. If most of the variance in a trace lives in stylistic and structural conventions rather than in reasoning, then most of the supervision is being spent on those conventions, and the model becomes very good at producing them while getting only the crumbs of the reasoning.


### Reframing: two different concepts of "quality"

There were two interpretations I had to weigh against each other, and they pointed in very different directions.

**The first:** data quality just doesn't matter at this scale. 0.5B parameters, ~1,700 examples the model can only extract the most basic signal ("produce something that looks like a math solution and ends with `####`") and any quality differences between traces sit below the noise floor.

This didn't quite fit. If quality genuinely didn't matter, Titanium should have landed near Normal. Both of them are correct, have valid math solution and have an answer at the end. Instead, Titanium landed exactly on the base model, which means training on it actively neutralised the signal that Normal extracted. 

**The second:** I had the concept of quality wrong.

I'd been optimising for traces that would be good for a human learner; clear, explicit, and well structured. The kind of solution I'd want a student to be shown. But a 0.5B model is not a human learner. It does not read. It does not benefit from named variables and explicit restatement the way a high school student does. It updates weights to predict next tokens.

For a small student, a "good" trace might mean something almost orthogonal. It might mean a trace that sits close to the distribution of math text the model saw in pretraining. With enough text to actually shift weights. With no special structure to consume capacity learning a new format.

Normal hits all three of those, almost by accident. It looks like math solutions on the open internet, which is where most of the model's pretraining math came from. It has enough prose to deliver meaningful supervision. It has no special structure to imitate. The thing that made Normal lazy from my teacher's perspective is precisely what made it effective from the model's.

I started calling this **student-learnable quality** to distinguish it from human readable quality. They aren't the same axis an they can actively trade off. And I'd been pushing hard on the first while quietly destroying the second.

---

### What scale changed

That was a satisfying story to tell myself. But I had to test it, and the obvious test was: if the binding constraint at 0.5B is really distributional accessibility i.e the trace's proximity to what the model has already seen, then at a larger scale, where the model has slack on capacity, the picture should change. Carefully written traces should start helping again, because the model can finally afford to absorb their structural overhead.

I ran the same four datasets at 3B. Then at 7B. Here are all the numbers:

<table class="table table-sm table-bordered">
  <thead>
    <tr>
      <th>Student</th>
      <th>Base</th>
      <th>Normal</th>
      <th>Gold</th>
      <th>Titanium</th>
      <th>Diamond</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0.5B</td>
      <td>33.3</td>
      <td><strong>49.7</strong></td>
      <td>47.3</td>
      <td>33.3</td>
      <td>45.7</td>
    </tr>
    <tr>
      <td>3B</td>
      <td>74.0</td>
      <td>85.3</td>
      <td><strong>87.7</strong></td>
      <td>70.0</td>
      <td>81.3</td>
    </tr>
    <tr>
      <td>7B*</td>
      <td>-</td>
      <td>88.3</td>
      <td><strong>90.3</strong></td>
      <td>81.3</td>
      <td>87.7</td>
    </tr>
  </tbody>
</table>

*The 7B base evaluation pipeline was misbehaving and produced uninterpretable numbers; I'm reporting only the SFT runs, which share an internally consistent pipeline.*

Two things flip between 0.5B and 3B. Gold overtakes Normal. And Titanium falls below the un-finetuned base. At 7B, Gold's lead widens, and Titanium recovers somewhat but still trails the rest of the field by about seven points.

The Gold result is the one I expected eventually, once I had the right frame. With more capacity, the model can absorb the structural overhead of pedagogical scaffolding, and the actual teaching content (restatement, completion check, explicit relations) finally becomes extractable. The picture I now carry around looks like this:

{% include figure.liquid path="assets/img/posts/trace_quality/fig_twofactor.png" title="Two factors, scaling differently" class="img-fluid rounded z-depth-1 mx-auto d-block" width="100%" max-width="760px" height="auto" zoomable=true %}

Two factors that score traces differently.

**Distributional accessibility** is how close the trace style sits to what the model saw in pretraining. Normal scores high. Diamond medium-high. Gold medium-low. Titanium very low.

**Teaching content** is how much actual reasoning signal lives in the trace beyond the bare requirement that it be correct. Gold is high (restatement and completion checks do real work). Normal and Diamond are medium. Titanium is low labels do not reason, they label.

At small scale, accessibility binds. The model cannot afford the distribution shift cost, so the ranking tracks accessibility almost perfectly: Normal > Gold > Diamond > Titanium.

At larger scale, accessibility has slack. The model has enough capacity to absorb new structure, the cost gets paid easily, and the ranking starts to track teaching content instead: Gold > Normal > Diamond > Titanium.

This is actually similar to the result found in the paper "Small models struggle to learn from strong reasoners " paper

This story makes three predictions:

First, **a schema with no teaching content should hurt at every scale, and the hurt should compound with capacity**. More capacity means more thorough format learning, which displaces math behaviour the base model already had. Titanium does exactly this, it is worse at 0.5B, worse than base at 3B, and at 7B it recovers only enough to still trail the other runs.

Second, **the crossover where Gold overtakes Normal should happen somewhere between 0.5B and 3B**. I haven't run intermediate sizes.

Third, **a pedagogical intervention that preserves the natural prose distribution should sit between Gold and Normal**, gaining some teaching content but giving up some of the explicit scaffolding. Diamond looks exactly like that.

---

### Did targeting failures actually work?

The original motivation for Gold was that the 0.5B made specific mistakes, and Gold's prompt explicitly addressed each of them. So there's a fair question to ask, separately from overall accuracy: did Gold actually reduce the targeted failure modes, even on runs where overall accuracy didn't move?

For the 3B student, where the deltas are large enough to read above the noise, the answer is mostly yes:

{% include figure.liquid path="assets/img/posts/trace_quality/fig_errors.png" title="Error count change by category, 3B student" class="img-fluid rounded z-depth-1 mx-auto d-block" width="100%" max-width="760px" height="auto" zoomable=true %}

Gold improves on the failure modes its prompt was designed to address. Relation inversions drop by 10 (Normal manages 7). State tracking by 7. Answer overwriting by 9. The categories where Gold helps most are precisely the ones the prompt asked the teacher to handle carefully. The intervention worked. The intervention worked. It just had to compete against accessibility costs that ate most of its margin.

Titanium tells the opposite story, it doesn't merely fail to teach, it creates errors that didn't exist before. Aggregation errors jump by 14. Relation inversions go up by 4 versus base. Loop degeneration appears as a category that essentially doesn't exist for the other datasets.

Diamond is the messiest. It improves operation omission by 7 (best of any dataset) but regresses on aggregation by 6. My read is that Diamond's compressed prose style sometimes packs too many sub operations into single sentences, and the model loses the thread when it tries to reproduce that compression at inference time. This is a guess. I haven't pulled enough traces to confirm.

The cleanest takeaway is that Gold's intervention did work as designed at 3B. The places it targeted are the places it improved. The reason Gold's overall accuracy gain over Normal is only +2.4 points isn't that the intervention was ineffective, it's that the gains kept getting partly cancelled by accessibility costs the small student couldn't fully afford.

---

### How problem difficulty interacts

One more breakdown I find interesting:

{% include figure.liquid path="assets/img/posts/trace_quality/fig_difficulty.png" title="Accuracy by problem difficulty, 3B student" class="img-fluid rounded z-depth-1 mx-auto d-block" width="100%" max-width="760px" height="auto" zoomable=true %}

On Easy problems, Gold wins by a comfortable margin: 92% to Normal's 87%. On Medium, Normal edges ahead (89% to 86%). On Hard, Gold and Normal tie at 75%.

The lazy reading is that Gold's scaffolding helps when problems are easy enough that the model can still use the scaffolding and stops helping when problems are hard enough that the scaffolding becomes overhead, but I don't think that's quite right. If scaffolding were just overhead, Gold should fall below Normal on hard problems, not tie. The pattern is more like: Gold helps where there's headroom and ties where neither approach has enough leverage. The intervention seems to be additive on top of a substrate of capability, it can convert "almost right" into "right," but it can't manufacture capability the model doesn't yet have.


Titanium sits flat at base level performance across all three difficulties. Its pathology isn't problem specific. The model just isn't learning math from these traces, regardless of how hard the math is.

---

### Where I might be wrong

There are some things that could break this story that I want to be explicit about:

**Token budget.** Different datasets have different total token counts:

{% include figure.liquid path="assets/img/posts/trace_quality/fig_tokens.png" title="Token budget per dataset" class="img-fluid rounded z-depth-1 mx-auto d-block" width="100%" max-width="760px" height="auto" zoomable=true %}

Gold has the most tokens, by a clear margin. Normal sits in the middle. Titanium and Diamond are the lean ones. You could try to tell a competing story where the rankings are basically about supervised token count i.e more tokens, more learning, but the data doesn't cooperate. Diamond has the fewest tokens of any dataset and still beats Titanium at every scale. Titanium has fewer tokens than Normal and fewer than Gold, but it loses to both by far more than the token gap would predict and at 3B, training on Titanium leaves the model worse than the base it started from, which no amount of "not enough supervision" explains. Whatever Titanium is losing on, it isn't supervision volume.

**Single seeds.** All these numbers are single seed runs. The 2.4 point Gold over Normal margin at 3B is exactly the kind of effect that could shrink or invert under reasonable seed variation. I don't know my noise floor. Until I do, treat the magnitudes as suggestive and the shape as the robust part; the small scale ranking flipping at larger scale, Titanium being uniformly bad, the directional consistency from 3B to 7B. The shape, I'd bet on. The decimal places, less so.

---

## What I'd like to try next

One consequence of this result is that we might be leaving some data on the table from targeted improvements on vanilla cot. Is there a world where we can train a model on the same dataset multiple times with improvements in the reasoning traces each round based on the observed failures of the previous round and without overfitting or collapsing the model?


## References

- Li et al., "Small Models Struggle to Learn from Strong Reasoners," [arXiv:2502.12143](https://arxiv.org/abs/2502.12143)
- Kim et al., "In Their Own Words: Reasoning Traces Tailored for Small Models Make Them Better Reasoners," [arXiv:2509.22230](https://arxiv.org/pdf/2509.22230)
- Costello et al., "Think, Prune, Train, Improve: Scaling Reasoning without Scaling Models," [arXiv:2504.18116](https://arxiv.org/abs/2504.18116)
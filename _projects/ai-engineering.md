---
layout: page
title: "AI Engineering: Training & Scaling"
description: "Implementing transformers, preparing training data, and evaluating multi-GPU training strategies on Delta."
img: assets/img/ai-engineering/training-scaling-card.svg
importance: 2
category: work
permalink: /projects/ai-engineering/
related_publications: false
_styles: |
  .ai-project-summary {
    padding: 1.25rem;
    margin: 1.5rem 0 2rem;
    border: 1px solid var(--global-divider-color);
    border-radius: .5rem;
  }
  .ai-project-summary p:last-child { margin-bottom: 0; }
  .ai-project-status { color: var(--global-text-color-light); font-size: .9rem; }
  .ai-project-figure { margin: 1.5rem 0; }
---

Through **AI Engineering** at CU Boulder, I implemented a transformer, prepared training data, and evaluated language models. My experiments explore how model size, training data, and evaluation methods affect performance, and how to turn those findings into practical engineering decisions.

<div class="ai-project-summary" markdown="1">

**Featured result: 21.6% lower Wikipedia evaluation loss through data curation.**

I tested sentence-formatting filters in a Spark data pipeline, then compared the resulting models across four evaluation corpora. My best prose-focused mix reduced Wikipedia loss from **1.8808 to 1.4747 nats per character**, while slightly increasing Python-code loss. The result illustrates both the value of data curation and its dependence on the target domain.

**Tools and methods:** Python, PyTorch, Spark, Opik, cross-domain evaluation, LLM judging, and Slurm.

</div>

## Improving training data and measuring the tradeoff

I worked with a pipeline that turns raw Common Crawl text into training data through normalization, chunking, deduplication, and feature-based filtering. Exact deduplication removed approximately **55% of the chunks** remaining at that stage.

For my experiments, I added sentence-ending punctuation and capitalization features and tested five data mixes. The best mix on Wikipedia combined these features with a minimum English stopword count. The baseline and this mix contained approximately **50 million training tokens** each.

<div class="ai-project-figure">
{% include figure.liquid path="assets/img/ai-engineering/data-curation-comparison.svg" alt="Bar chart comparing baseline and prose-filtered evaluation loss on Wikipedia, TinyStories, Shakespeare, and Python code. The filtered mix improves the first three and slightly worsens Python code." class="img-fluid rounded" caption="A3 results: baseline versus the best mix on Wikipedia. Lower loss is better; values are nats per character." %}
</div>

The filtered mix also improved TinyStories loss, but Python-code loss rose from **3.4032 to 3.4733**. I also tried restricting prose to `.edu` hosts. That left only **1.7 million tokens**, so I treated it as an example of overfiltering rather than a comparison at the same data budget.

**What I learned:** a useful filter should be judged against the intended domain and multiple evaluation sets. A cleaner-looking dataset does not guarantee better performance everywhere.

## Evaluating models beyond a single score

I evaluated a ladder of Shakespeare-trained language models across Shakespeare, TinyStories, Wikipedia, and Python code, then compared the base model with an identically configured model trained on TinyStories. I used Opik to inspect generation traces and judge scores.

<div class="ai-project-figure">
{% include figure.liquid path="assets/img/ai-engineering/cross-domain-evaluation.png" alt="Evaluation heatmap showing loss for five model sizes across four corpora, with out-of-vocabulary character deletion rates listed for each corpus." class="img-fluid rounded" caption="A2 cross-domain evaluation. The larger models do not consistently improve every column, and the base checkpoint is not the lowest-loss model on Shakespeare in this evaluation." %}
</div>

I compared next-character loss with LLM judgments of grammar, coherence, and style, using **50 generations per model**. The TinyStories model received higher pointwise grammar and coherence scores, while pairwise preferences followed the requested writing-style rubric. This made the question of which model was "better" depend on what the evaluation actually rewarded.

I also tested the judge by paraphrasing its rubric and reordering the scoring dimensions. With the scoring dimensions reordered, the base model's mean grammar score changed from **2.94 to 2.34**.

**What I learned:** judge prompts are part of the measurement system. I need to check their sensitivity and examine multiple measures before trusting a model ranking.

## Implementing and training a transformer

Within the course scaffold, I implemented the core components of a decoder-only transformer in PyTorch: causal multi-head attention, a feed-forward network, pre-normalized residual blocks, and the model's forward pass. I trained it on character-level Shakespeare text and compared four model sizes at a fixed training-token budget.

<div class="ai-project-figure">
{% include figure.liquid path="assets/img/ai-engineering/training-curves.png" alt="Training loss continues to fall while validation loss levels off and eventually rises during the base model's training run." class="img-fluid rounded" caption="A1 base-model training curves: yellow is training loss; orange is validation loss. The horizontal axis shows steps in hundreds, and the vertical axis shows loss. Their separation helped me identify overfitting." %}
</div>

The model-size study showed diminishing improvements in validation loss across the sizes I tested. I used those observations to consider whether more model capacity or more training data would be a useful next experiment.

**What I learned:** implementing the architecture and inspecting its learning curves gave me a clearer understanding of the behavior behind a training run.

## Scaling training on Delta

<div class="ai-project-summary" markdown="1">

**Implementation completed; benchmark results pending.**

For the distributed-training assignment, I have completed the code on the Delta server and submitted Slurm jobs. As of October 7, 2026, I am waiting for those runs to finish. I will add throughput, scaling efficiency, memory use, and an analysis of the tradeoffs once the results are available.

</div>

---

<p class="ai-project-status">Fall 2026 coursework at CU Boulder. Engineering results are from my A1-A3 reports; A4 measurements are pending. The assignments build on course-provided scaffolding, with my implementation and analysis described above.</p>

[AI Engineering course overview](https://csci5942.github.io/)

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
  .ai-training-benchmarks .table-responsive { margin: 1.25rem 0; }
  .ai-training-benchmarks table { width: 100%; min-width: 32rem; font-size: .9rem; }
  .ai-training-benchmarks th,
  .ai-training-benchmarks td {
    padding: .65rem .6rem;
    border-bottom: 1px solid var(--global-divider-color);
  }
---

Through **AI Engineering** at CU Boulder, I studied the training stack from transformer implementation and data preparation to distributed training on Delta. My experiments connect model quality with the practical constraints of GPU memory, communication, and training time.

<div class="ai-project-summary" markdown="1">

**Training foundations, measured scaling, and model evaluation.**

I implemented a decoder-only transformer and benchmarked distributed training configurations, including a **4.04-billion-parameter model across eight GPUs**. Overlapped data parallelism achieved **92.0% scaling efficiency on four GPUs** in a separate 406M-parameter experiment. I also tested data curation and evaluation methods: my best prose-focused data mix reduced Wikipedia evaluation loss by **21.6%**, with a small regression on Python code.

**Tools and methods:** Python, PyTorch, Spark, Opik, Slurm, data/tensor/pipeline parallelism, cross-domain evaluation, and LLM judging.

</div>

## Implementing and training a transformer

Within the course scaffold, I implemented the core components of a decoder-only transformer in PyTorch: causal multi-head attention, a feed-forward network, pre-normalized residual blocks, and the model's forward pass. I trained it on character-level Shakespeare text and compared four model sizes at a fixed training-token budget.

<div class="ai-project-figure">
{% include figure.liquid path="assets/img/ai-engineering/training-curves.png" alt="Training loss continues to fall while validation loss levels off and eventually rises during the base model's training run." class="img-fluid rounded" caption="A1 base-model training curves: yellow is training loss; orange is validation loss. The horizontal axis shows steps in hundreds, and the vertical axis shows loss. Their separation helped me identify overfitting." %}
</div>

The model-size study showed diminishing improvements in validation loss across the sizes I tested. I used those observations to consider whether more model capacity or more training data would be a useful next experiment.

**What I learned:** implementing the architecture and inspecting its learning curves gave me a clearer understanding of the behavior behind a training run.

<div class="ai-training-benchmarks" markdown="1">

## Scaling training on Delta

I extended the course scaffold with gradient synchronization and communication overlap for data parallelism, tensor-parallel layers, and a one-forward/one-backward (1F1B) pipeline schedule. I ran the experiments through Slurm on Delta's NVIDIA A40 GPUs and compared step time, training throughput, and reported peak memory.

### Overlapping communication with computation

For a **406M-parameter model**, overlapped data parallelism reached **89,543 training tokens/second on four GPUs**, a **3.68x speedup** over its one-GPU baseline and **92.0% scaling efficiency**. The naive four-GPU version reached 86,384 tokens/second and 87.6% efficiency.

I compared the measured gap from ideal scaling with an estimate based on gradient size and all-reduce bandwidth. The estimated communication time was approximately **0.155 seconds**, versus **0.188 seconds** of measured overhead for naive data parallelism. Overlap shortened the four-GPU step from **1.517 to 1.464 seconds**. This helped me connect a performance measurement to the communication work behind it.

### Choosing between throughput and memory

For an **838M-parameter model** at a fixed global batch, data parallelism delivered the highest throughput, while tensor parallelism reduced the reported peak memory.

<div class="table-responsive" markdown="1">

| Configuration                         | Training tokens/s | Step time (s) | Peak memory (GiB) |
| :------------------------------------ | ----------------: | ------------: | ----------------: |
| One GPU                               |            11,649 |         11.25 |             18.55 |
| Data parallel: 4 GPUs                 |        **41,949** |      **3.12** |             18.55 |
| Data parallel: 2 x tensor parallel: 2 |            21,681 |          6.05 |             10.50 |
| Tensor parallel: 4 GPUs               |            10,834 |         12.10 |          **6.34** |

</div>

**My decision:** use data parallelism for this model on these GPUs, because the model's training footprint already fit on one A40. Tensor parallelism offered a smaller memory footprint, but its communication cost made it slower in this experiment.

### Scheduling a pipeline to fit in memory

With four pipeline stages and **32 micro-batches**, the all-forward/all-backward schedule ran out of memory at a reported peak above 44.42 GiB. **1F1B completed at 6.71 GiB**. At 16 micro-batches, 1F1B used 8.35 GiB versus 21.12 GiB for all-forward/all-backward, with similar measured idle fractions.

**My decision:** use 1F1B to manage the lifetime of saved activations. Increasing the number of micro-batches reduced measured idle time, but the schedule determined whether that configuration fit in memory.

### Benchmarking a model larger than one GPU's training capacity

I tested the course's **4.04B-parameter configuration on eight A40 GPUs across two Delta nodes**. Pure data parallelism failed to fit, so I compared tensor and pipeline sharding configurations.

<div class="table-responsive" markdown="1">

| Configuration (DP / TP / PP) | Training tokens/s | Step time (s) | Peak memory (GiB) |
| :--------------------------- | ----------------: | ------------: | ----------------: |
| 8 / 1 / 1                    |     Out of memory |             - |            >44.42 |
| 4 / 2 / 1                    |            10,236 |         12.81 |             38.15 |
| 1 / 2 / 4, 1F1B              |            10,964 |         11.95 |         **15.54** |
| 1 / 1 / 8, 1F1B              |        **14,322** |      **9.15** |             20.56 |

</div>

DP, TP, and PP denote the data-, tensor-, and pipeline-parallel degrees. This table shows selected configurations from my eight-GPU sweep; every row uses eight GPUs.

**My decision:** eight-stage pipeline parallelism with 1F1B was the fastest configuration I tested. It avoided the gradient and tensor-parallel all-reduces of the other mappings while keeping memory within the GPU limit. The result shows why a configuration should be selected for the particular model and hardware. These are short training benchmarks; the 4.04B model was not trained to convergence.

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

---

<p class="ai-project-status">Fall 2026 coursework at CU Boulder. Results are from my completed A1-A4 reports. The assignments build on course-provided scaffolding, with my implementation and analysis described above. Distributed-training results are specific to the tested model configurations and Delta hardware.</p>

[AI Engineering course overview](https://csci5942.github.io/)

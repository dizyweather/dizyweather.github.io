---
layout: page
title: "AI Engineering: LLM Pipeline Foundations"
description: "Hands-on foundations in transformer architecture, model evaluation, large-scale data processing, and distributed training."
img: assets/img/ai-engineering/pipeline-foundations-card.svg
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
  .ai-training-benchmarks details {
    margin: 1.25rem 0;
    padding: 1rem;
    border: 1px solid var(--global-divider-color);
    border-radius: .5rem;
  }
  .ai-training-benchmarks summary { cursor: pointer; font-weight: 600; }
---

I'm currently taking [**CSCI 5942: AI Engineering**](https://csci5942.github.io/), a graduate-level course at CU Boulder taught by **Professor Christoffer Heckman and Professor Mark Zhao**.

<div class="ai-project-summary" markdown="1">

**Foundations across the LLM training pipeline.**

- **Architecture:** implemented core components of a decoder-only transformer and inspected its learning curves.
- **Evaluation:** compared models across domains and investigated sensitivity to LLM-judge rubrics.
- **Data:** processed and filtered training text with a Spark pipeline, then measured the effects on model quality.
- **Systems:** benchmarked data, tensor, and pipeline parallelism on Delta to understand speed and memory tradeoffs.

**Tools and methods:** Python, PyTorch, Spark, Opik, Slurm, data/tensor/pipeline parallelism, cross-domain evaluation, and LLM judging.

</div>

## A1: Transformer architecture and training

Within the course scaffold, I implemented causal multi-head attention, a feed-forward network, pre-normalized residual blocks, and the forward pass of a decoder-only transformer in PyTorch. I trained it on character-level Shakespeare text and compared four model sizes at a fixed training-token budget.

<div class="ai-project-figure">
{% include figure.liquid path="assets/img/ai-engineering/training-curves.png" alt="Training loss continues to fall while validation loss levels off and eventually rises during the base model's training run." class="img-fluid rounded" caption="A1 training (yellow) and validation (orange) loss, with steps in hundreds. The widening gap indicates overfitting." %}
</div>

Larger models gave diminishing improvements in validation loss; the training/validation gap also revealed overfitting.

**What I learned:** training loss alone isn't enough. Architecture and learning curves help me judge whether more model capacity improves generalization.

## A2: Model evaluation

I evaluated models across four corpora, then compared Shakespeare- and TinyStories-trained models using **50 generations each**. I used Opik to inspect traces and LLM judgments of grammar, coherence, and style.

<div class="ai-project-figure">
{% include figure.liquid path="assets/img/ai-engineering/cross-domain-evaluation.png" alt="Evaluation heatmap showing loss for five model sizes across four corpora, with out-of-vocabulary character deletion rates listed for each corpus." class="img-fluid rounded" caption="A2 loss across four corpora: larger models do not consistently perform better." %}
</div>

Pointwise scores favored TinyStories for grammar and coherence; pairwise preferences followed the requested style. Changing the judge rubric also changed scores.

**What I learned:** lower loss on one dataset doesn't necessarily mean better generated text or stronger performance in other domains. LLM judges can assess grammar, coherence, and style, but their rankings depend on the rubric and prompt wording. I learned to compare multiple measures before trusting a model ranking.

## A3: Processing training data at scale

I worked with a **Spark pipeline for Common Crawl text**, covering normalization, chunking, deduplication, and filtering. I added prose-quality features and compared filtered mixes with a baseline at **the same training-token budget**.

<div class="ai-project-figure">
{% include figure.liquid path="assets/img/ai-engineering/data-curation-comparison.svg" alt="Bar chart comparing baseline and prose-filtered evaluation loss on Wikipedia, TinyStories, Shakespeare, and Python code. The filtered mix improves the first three and slightly worsens Python code." class="img-fluid rounded" caption="A3 results: baseline versus the best mix on Wikipedia. Lower loss is better; values are nats per character." %}
</div>

With prose-focused filters, Wikipedia evaluation loss fell **21.6%**, while Python-code loss increased slightly. More aggressive filtering also left too little training data for a fair comparison.

**What I learned:** data quantity alone isn't enough. Duplicates and irrelevant text can reduce a dataset's usefulness, while aggressive filtering can remove valuable data. Curation requires balancing quality, diversity, and volume against the target domain.

<div class="ai-training-benchmarks" markdown="1">

## A4: Distributed training on Delta

I studied how training can be distributed across GPUs by replicating a model over different data, sharding its tensors, or splitting its layers into pipeline stages. I extended the course scaffold with overlapped gradient synchronization, tensor-parallel layers, and a one-forward/one-backward (1F1B) pipeline schedule, then ran benchmarks through Slurm on Delta's NVIDIA A40 GPUs.

For an **838M-parameter model** at a fixed global batch, data parallelism gave the highest throughput, while tensor parallelism reduced reported peak memory:

<div class="table-responsive" markdown="1">

| Configuration                         | Training tokens/s | Step time (s) | Peak memory (GiB) |
| :------------------------------------ | ----------------: | ------------: | ----------------: |
| One GPU                               |            11,649 |         11.25 |             18.55 |
| Data parallel: 4 GPUs                 |        **41,949** |      **3.12** |             18.55 |
| Data parallel: 2 x tensor parallel: 2 |            21,681 |          6.05 |             10.50 |
| Tensor parallel: 4 GPUs               |            10,834 |         12.10 |          **6.34** |

</div>

**What I learned:** choose a strategy for the model and hardware available. Data parallelism was the better throughput choice when this model already fit on one A40; tensor and pipeline sharding became useful for managing memory. More GPUs introduce communication and scheduling costs as well as capacity.

<details markdown="1">
<summary>Additional distributed-training benchmarks</summary>

### Overlapping communication with computation

For a **406M-parameter model**, overlapped data parallelism reached **89,543 training tokens/second on four GPUs**, a **3.68x speedup** over its one-GPU baseline and **92.0% scaling efficiency**. The naive four-GPU version reached 86,384 tokens/second and 87.6% efficiency.

I compared the measured gap from ideal scaling with an estimate based on gradient size and all-reduce bandwidth. The estimated communication time was approximately **0.155 seconds**, versus **0.188 seconds** of measured overhead for naive data parallelism. Overlap shortened the four-GPU step from **1.517 to 1.464 seconds**. This helped me connect a performance measurement to the communication work behind it.

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

</details>

</div>

## Future work

### Efficient model adaptation

**Planned coursework:** I plan to compare full fine-tuning with LoRA on pretrained GPT-2, measuring task quality, GPU memory, training time, and retention of general capabilities. I will investigate how adapter rank and placement affect these tradeoffs, and implement adapter merging for inference.

This work will extend the training experiments to adapting a pretrained model under a limited resource budget. The upcoming [A5 assignment](https://github.com/csci5942/assignments/blob/main/assignment-5/README.md) compares these approaches on the E2E natural-language-generation task.

### Inference serving

**Planned extension:** I want to serve a trained or adapted model through an inference API and measure how it behaves under different request loads. I will document the model, hardware, and serving configuration, then compare time to first token, output-token throughput, latency, and GPU memory use.

This is a future milestone. I will add an implementation and measured results once it is completed.

---

<p class="ai-project-status">Fall 2026 coursework at CU Boulder. Results are from my completed A1-A4 reports; model adaptation and inference serving are planned. The assignments build on course-provided scaffolding, with my implementation and analysis described above. Distributed-training results are specific to the tested model configurations and Delta hardware.</p>

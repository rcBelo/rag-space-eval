---
layout: default
title: "A Systematic Evaluation of RAG and Language Models for Space Operations"
---

<p align="center">
  <b>A Systematic Evaluation of Retrieval-Augmented Generation and Language Models for Space Operations</b>
</p>

<p align="center">
  Ruben Belo* · Marta Guimarães · Cláudia Soares
</p>

<p align="center">
  NOVA LINCS &nbsp;·&nbsp; Neuraspace &nbsp;·&nbsp; Technical University of Munich
</p>

<p align="center">
  <a href="paper.pdf">[Paper]</a> &nbsp;|&nbsp; <a href="assets/poster.pdf">[Poster]</a>
</p>

---

## Overview

Space operations generate vast, heterogeneous documentation that engineers and operators must navigate under time pressure. This paper systematically evaluates **Retrieval-Augmented Generation (RAG) pipelines** for the space domain, comparing retrieval strategies, embedding models, rerankers, and LLM answer quality to assess accuracy, relevance, and reliability.

All models evaluated are **open-source** and deployable in private, secure environments — a critical requirement for space operations.

---

## Method

A standard RAG pipeline consists of two main stages: a **retriever**, which selects relevant passages from a document corpus, and a **generator** (LLM), which produces an answer conditioned on the retrieved context.

![RAG Pipeline](assets/pipeline.png)

We evaluate the following components:

- **Chunking strategies** — 512-token vs. 2,000-token passage sizes
- **Retrievers** — BM25 and 8 state-of-the-art embedding models from the [MMTEB leaderboard](https://huggingface.co/spaces/mteb/leaderboard)
- **Rerankers** — BGE-M3, GTE-reranker-base, Jina-reranker-v2 (used as pseudo ground truth)
- **Generator** — Llama 3 8B evaluated on the ESA SpaceQA dataset

---

## Results

### Retrieval

Reranking **consistently reduces irrelevant passages** and increases the proportion of highly relevant ones across all Top-K settings and chunk sizes. Using 512-token chunks with reranking yields the best results.

| Setting | Highly Relevant (Retriever) | Highly Relevant (Reranker) |
|---|---|---|
| 2,000-token chunks, Top-3 | 39.66% | 44.76% |
| 512-token chunks, Top-3 | 42.54% | 48.37% |

**Model selection guidance:**
- *With a reranker* → prioritise high-recall retrievers (e.g. BM25); the reranker handles relevance refinement.
- *Without a reranker* → prioritise NDCG; passage ranking quality matters more (e.g. BGE-M3, Qwen2).

### Answer Quality

Llama 3 8B was evaluated on the ESA SpaceQA dataset (60 Q&A pairs) with 1 relevant passage + 4 noise passages per question.

| Metric | Score |
|---|---|
| Answer Faithfulness | 3.92 / 5 |
| Answer Relevance | 4.02 / 5 |
| Noise Robustness | 4.52 / 5 |
| Answer Accuracy (with RAG) | 56 / 60 |
| Answer Accuracy (without RAG) | 3 / 60 |

RAG improves answer accuracy from **3/60 to 56/60**, demonstrating the critical role of retrieved context.

---

## Failure Analysis

The 4 incorrect answers share common patterns:

- **Implicit entity linking** — relevant information is present but not explicitly tied to the queried entity.
- **Missing chunk content** — critical details fall outside the retrieved passage.
- **Attention bias** — the model focuses on the first entities mentioned and disregards later ones.
- **Conservative generation** — the model avoids speculation, defaulting to "not in context" to reduce hallucination risk.

These failures point to retrieval design as the key bottleneck: richer metadata (mission name, type, equipment) and better chunking strategies can address most cases.

---

## Citation

```bibtex
@inproceedings{belo2026rag,
  title     = {A Systematic Evaluation of Retrieval-Augmented Generation and Language Models for Space Operations},
  author    = {Belo, Ruben and Guimarães, Marta and Soares, Cláudia},
  booktitle = {CVPR Workshop},
  year      = {2026}
}
```

---

<p align="center">
  Supported by <a href="https://nova-lincs.di.fct.unl.pt/">NOVA LINCS</a> (FCT IP, UID/04516) and the Neuraspace AI Fights Space Debris project (co-funded by Recovery and Resilience Plan and NextGenerationEU).
</p>

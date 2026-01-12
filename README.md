# 📚 NarrativeNexus  
### Logic-Driven Narrative Consistency Engine

> **A systems-level NLP project for detecting logical and causal inconsistencies in long-form narratives using hybrid retrieval, evidence validation, and controlled reasoning.**

---

## 🔹 Project Summary

- Designed an evidence-driven NLP system to detect logical inconsistencies in long-form narratives (100k+ tokens).
- Built hybrid retrieval pipelines combining global document context with entity-centric evidence modeling.
- Implemented a meta-learning classifier to prevent hallucinations in LLM-based reasoning systems.
- Framed narrative consistency as a supervised classification problem with explainable outputs.

---

![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![AI](https://img.shields.io/badge/Models-Ollama%20%7C%20Phi--3-purple)
![License](https://img.shields.io/badge/License-MIT-green)
![Status](https://img.shields.io/badge/Status-Stable-success)

---

## 🧠 Problem Overview

Modern language models perform well on **local text understanding** tasks such as summarization and question answering.  
However, they struggle with **global consistency reasoning** in long-form narratives, where meaning emerges cumulatively over time.

In long documents:
- Early events impose constraints on later outcomes  
- Characters evolve and make irreversible commitments  
- Certain futures become logically or causally impossible  

Despite this, language models often rely on **surface-level plausibility**, producing outputs that sound coherent but violate previously established facts.

---

## 🎯 Project Objective

**NarrativeNexus** treats narrative consistency as a **structured reasoning and classification problem**, not a text generation task.

### Input
- A complete long-form narrative (full text, no truncation)
- A newly written, plausible hypothetical backstory for a central character

### Output
- `1` → Backstory is **consistent** with the narrative  
- `0` → Backstory **contradicts** narrative constraints  

All decisions are **evidence-grounded** and supported by signals aggregated across the entire text.

---

## 💡 Core Design Philosophy

> **Narrative consistency is an evidence validation problem, not a generation problem.**

Instead of trusting an LLM end-to-end, the system:
1. Retrieves all relevant narrative constraints
2. Measures the strength and coverage of supporting evidence
3. Allows reasoning only when sufficient evidence exists

This design prioritizes robustness, explainability, and hallucination resistance.

---

## 🧩 System Approach

NarrativeNexus is built as a **Hybrid Retrieval + Meta-Learning pipeline** with three stages.

---

### 1️⃣ Hybrid Evidence Retrieval

**Global Retrieval**
- Vector index over the entire narrative
- Captures world-level constraints such as timelines, major events, and causal structure

**Character-Centric Dossiers**
- Dedicated indices per character
- Tracks names, aliases, titles, and pronouns (He / She / They)

This resolves a key failure mode of standard RAG systems where critical evidence lacks explicit entity mentions.

---

### 2️⃣ Evidence Quality Assessment (Meta-Learner)

A **Random Forest classifier** evaluates retrieved evidence using:
- Similarity scores
- Evidence density
- Distribution across chapters

**Purpose**
- Acts as a hallucination firewall
- Rejects weak or sparse evidence early
- Prevents LLM reasoning on noise

---

### 3️⃣ Controlled Reasoning (Optional LLM)

When evidence quality is high:
- A local language model (Phi-3 / Llama-3 via Ollama) is invoked
- Reasoning is constrained to:
  - Temporal consistency
  - Causal compatibility
  - Character state (alive/dead, location, relationships)

The system outputs a **binary verdict with a concise rationale**, not free-form text.

---

## 🏗️ System Architecture

```mermaid
graph TD
    A[Raw Narrative Texts] --> B[Preprocessing]
    B --> C{Indexing Strategy}

    C -->|Global| D[FAISS Global Index]
    C -->|Character| E[FAISS Character Dossiers]

    F[Hypothetical Backstory] --> G[Hybrid Retrieval]
    G --> D
    G --> E

    H[Retrieved Evidence] --> I[Feature Extraction]
    I --> J[Random Forest Meta-Learner]

    J -->|High Confidence| K[Final Verdict]
    J -->|Ambiguous| L[Local LLM Reasoning]
    L --> K

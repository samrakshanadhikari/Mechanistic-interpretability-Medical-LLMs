# Mechanistic Interpretability for Medical LLMs

This repository contains code and experiments for identifying and validating **demographic bias in medical large language models (LLMs)** using **mechanistic interpretability techniques**.  
The project focuses on understanding how **Chain-of-Thought (CoT) prompting** affects internal gender bias during clinical reasoning.

---

## Project Overview

Recent work has shown that medical LLMs can produce biased diagnostic outcomes when patient demographics (e.g., gender) change, even when symptoms remain the same. At the same time, these models often generate gender-neutral Chain-of-Thought explanations, creating an *illusion of fairness*.

This project goes beyond surface-level evaluation and asks:

> **Does Chain-of-Thought prompting reduce bias internally, or does it simply hide it behind plausible reasoning?**

To answer this, we analyze **internal model activations** during reasoning using mechanistic interpretability methods such as **Activation Patching** and **Edge Attribution Patching (EAP)**.

---

## Data

- **Dataset:** MIMIC-IV  
- **Notes used:** Brief Hospital Course (BHC) discharge summaries  
- **Format:** JSON (all-json file)

We filter BHC notes for three clinical conditions:
- Heart Failure
- Asthma
- Depression

Only **English-language, ICU-focused** clinical notes are used.  
Gender indicators are removed to create **gender-neutral baselines**, enabling controlled activation patching.

---

## What This Code Does

### 1. Negation-aware clinical filtering
The code identifies disease mentions while excluding negated cases (e.g., *“no asthma”*, *“denies depression”*) using a window-based negation detector.

### 2. Weak-label scoring
Each clinical note is assigned a confidence score based on:
- Diagnosis terms  
- Supporting clinical findings  
- Disease-specific medications  

This is done separately for:
- Heart Failure
- Asthma
- Depression

### 3. High-confidence cohort selection
Notes are:
- Scored  
- Ranked  
- Thresholded  

to extract the **top 100 high-confidence cases per disease** for downstream analysis.

### 4. Manual review support
Sample cases are printed to support **human or LLM-based validation**, aligning with best practices in weak supervision.

---

## Models Used

We use **open-weight transformer-based LLMs**, including:
- **Qwen 2.5 / Qwen 3**
- **Gemma 3**

These models are chosen because they allow **direct access to internal activations**, which is required for mechanistic interpretability.

As a result, findings may not directly transfer to **closed-source models** (e.g., GPT-4, Claude, Gemini), where internal representations are inaccessible.

---

## How to Clone and Run

### 1. Clone the repository

```bash
git clone https://github.com/samrakshanadhikari/Mechanistic-interpretability-Medical-LLMs.git
cd Mechanistic-interpretability-Medical-LLMs
```

### 2. Prepare the data

- Obtain access to MIMIC-IV following PhysioNet guidelines.

- Extract Brief Hospital Course (BHC) discharge notes.

- Save them as a JSON (all-json) file, for example:
```text
train_4000_600_chars.json
```
### 3. Run the filtering script:
```bash
python filterout_100.py
```
This will generate:

- hf_top_100.json

- asthma_top_100.json

- depression_top_100.json

### Repository Structure

- filterout_100.py — disease filtering and scoring logic

- *_top_100.json — high-confidence clinical cohorts

- README.md — project overview and documentation

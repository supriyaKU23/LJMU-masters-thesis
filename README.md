# Prompt-Based Job Description Summarization and Role Categorization using RAG
An experimental pipeline for evaluating prompt-based summarization of job descriptions and role categorization, with and without RAG.
The project compares different prompting strategies and model configurations without training any machine learning models.

Table of Contents
Overview
Research Motivation
Dataset
Reference Summary Generation
Pipeline Architecture
Summarization Experiments
Evaluation Framework
Project Structure
Usage
Notes
Overview

This project implements a structured experimentation pipeline to evaluate how effectively Large Language Models (LLMs) can:

Summarise job descriptions into structured outputs
Preserve key skills and responsibilities
Reduce hallucinations
Improve factual grounding using RAG

The system is fully prompt-based, meaning:

No model training
No fine-tuning
Only prompt engineering + retrieval

All experiments were conducted on a subset of 100 job descriptions for consistent and controlled evaluation.

Research Motivation

Job descriptions are:

Unstructured
Redundant
Inconsistent
Often noisy

This makes automated processing difficult for:

Recruitment systems
Job search platforms
Skill extraction pipelines

This project explores:

Whether prompt-based summarization is sufficient
Whether RAG improves factual accuracy
Which prompting strategy performs best
Dataset

The project uses a processed dataset derived from the Djinni Job Descriptions dataset.

Each record includes:

Job ID (id)
Position
Long Description
Reference Summary

The dataset used for all experiments is already included in this repository:

data/reference_summaries/djinni_with_reference_summaries.csv

From this dataset, 100 samples were selected for all summarization experiments to ensure:

Fair comparison across models and prompts
Controlled evaluation conditions
Faster experimentation and reproducibility
Reference Summary Generation

Reference summaries were generated using Google Gemini.

Configuration:

GEMINI_MODEL_NAME = "gemini-3-flash-preview"
GEMINI_TEMPERATURE = 0.0
GEMINI_MAX_OUTPUT_TOKENS = 700

Approach:

Strict prompting
Deterministic generation (temperature = 0.0)
Structured output enforced
No hallucinated information

These summaries act as ground truth for evaluation.

Pipeline Architecture

The summarization pipeline follows these steps:

Job Description
        │
        ▼
Text Cleaning
        │
        ▼
Chunking (Fixed-size with overlap)
        │
        ▼
Embedding (all-MiniLM-L6-v2)
        │
        ▼
ChromaDB Vector Store
        │
        ▼
Top-K Retrieval (RAG only)
        │
        ▼
Prompt Construction
        │
        ▼
LLM Generation
        │
        ▼
Evaluation Metrics
Summarization Experiments

The goal is to generate structured summaries with sections:

Role Overview
Responsibilities
Skills
Tools
Experience
Prompting Strategies

The following prompt types are evaluated:

Zero-shot prompting
Few-shot prompting
Chain-of-thought prompting
Style-based prompting
Self-consistency prompting
Experiment Variants
Non-RAG Summarization
Direct job description → summary
No retrieval
Prompt-only approach
RAG-based Summarization
Job description is split into chunks
Relevant chunks retrieved using embeddings
Retrieved context used to guide summarization
Self-Consistency Summarization
Multiple candidate summaries generated (3–5 candidates per job description)
Candidates scored based on:
Consistency
Completeness
Grounding
Faithfulness
Best summary selected
Evaluation Framework

Each generated summary is evaluated against the reference summary using:

Core Metrics
ROUGE-L
Semantic Similarity
Compression Ratio
Skill-Based Metrics
IRR (Information Retrieval Recall)
Top-K Skill Coverage
Global Skill Overlap
Hallucination Metrics
Hallucination Rate
Hallucination Prevalence
Hallucination Density
RAG-Specific Metrics
Grounding Score
Faithfulness Score
Project Structure
.
├── outputs
│   ├── final_comparision
│   │   └── summarization
│   │       ├── rag
│   │       │   └── rag_summarization_final_metrics_all_experiments.csv
│   │       └── non_rag
│   │           └── non_rag_summarization_final_metrics_all_experiments.csv
│   │
│   └── experiments
│       ├── summarization_with_RAG
│       │   └── experiment folders
│       └── summarization_without_RAG
│           └── experiment folders
│
├── combining_outputs
│   └── combining_outputs_of_experiments.ipynb
│
├── data
│   └── reference_summaries
│       └── djinni_with_reference_summaries.csv
│
├── experiments
│   ├── summarization_with_rag
│   │   └── notebooks
│   └── summarization_without_rag
│       └── notebooks
│
└── docs
    └── experiment_lists
        ├── summarization_without_rag.xlsx
        └── summarization_with_rag.xlsx
Usage

All experiments are executed via Google Colab notebooks.

Steps:

Mount Google Drive
Open experiment notebook
Configure experiment settings
Run pipeline

Each notebook handles:

Data preprocessing
RAG setup
Prompting
Evaluation
Output storage
Notes
Experiments are performed on 100 job descriptions
No requirements.txt is included (executed in Google Colab)
API keys are managed via Colab Secrets
Each experiment stores outputs separately for reproducibility
The dataset already includes reference summaries and is ready to use

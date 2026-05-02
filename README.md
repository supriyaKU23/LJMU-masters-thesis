# Prompt-Based Job Description Summarization and Role Categorization using RAG
An experimental pipeline for evaluating prompt-based summarization of job descriptions and role categorization, with and without RAG.
The project compares different prompting strategies and model configurations without training any machine learning models.

There are two tasks as part of this research.

## TASKS
- Task A: Summarization
- Task B: Role Categorization

# Summarization Task
## Overview

This project evaluates how effectively Large Language Models (LLMs) can:

- Summarise job descriptions into structured outputs  
- Preserve key skills and responsibilities  
- Reduce hallucinations  
- Improve factual grounding using RAG  

All experiments are **prompt-based** (no training or fine-tuning).

**Note:** Experiments were conducted on a subset of **100 job descriptions**.

---

## Research Motivation

Job descriptions are:

- Unstructured  
- Redundant  
- Inconsistent  

This project explores:

- Effectiveness of prompt-based summarization  
- Impact of RAG on factual accuracy  
- Comparison of different prompting strategies  

---

## Dataset

The dataset is derived from the Djinni Job Descriptions dataset.
https://huggingface.co/datasets/lang-uk/recruitment-dataset-job-descriptions-english 

Each record in the dataset contains the following fields:

- Position  
- Long Description  
- Company Name  
- Exp Years  
- Primary Keyword  
- English Level  
- Published  
- Long Description_lang  
- id  
- __index_level_0__  

For this research, the primary column used is:

- Long Description → input for summarization  

Additionally, a reference summary column is included:

- Reference_Summary → used as ground truth for evaluation  

Dataset used in experiments: 
data/reference_summaries/djinni_with_reference_summaries.csv


---

### Dataset Schema

Each record contains the following fields:

| Column Name | Description |
|------------|------------|
| Position | Job title |
| Long Description | Full job description text |
| Company Name | Name of the company |
| Exp Years | Required experience |
| Primary Keyword | Key role-related keyword |
| English Level | Required English proficiency |
| Published | Job posting date |
| Long Description_lang | Language of the job description |
| id | Unique identifier |
| __index_level_0__ | Index column |

### Additional Column Created

| Column Name | Description |
|------------|------------|
| Reference_Summary | Ground truth summary used for evaluation |


### Reference Summary Generation

Reference summaries were generated using Google Gemini.

**Configuration:**
GEMINI_MODEL_NAME = "gemini-3-flash-preview"
GEMINI_TEMPERATURE = 0.0
GEMINI_MAX_OUTPUT_TOKENS = 700

**Approach:**

- Strict prompting  
- Deterministic output (temperature = 0.0)  
- Structured format enforced  
- No hallucinated information  

---
## Pipeline Architecture
Job Description
↓
Text Cleaning
↓
Chunking
↓
Embedding (MiniLM)
↓
ChromaDB
↓
Top-K Retrieval (RAG)
↓
Prompt Construction
↓
LLM Generation
↓
Evaluation


---

## Summarization Experiments

Structured output format:

- Role Overview  
- Responsibilities  
- Skills  
- Tools  
- Experience  

### Prompt Types

- Zero-shot  
- Few-shot  
- Chain-of-thought  
- Style-based  
- Self-consistency  

---

## Experiment Variants

### Non-RAG

- Direct JD → Summary  
- Prompt-only  

### RAG

- JD → Chunking → Retrieval → Summary  

### Self-Consistency

- Generate 3–5 summaries  
- Score candidates  
- Select best output  

---

## Evaluation Metrics

### Core Metrics

- ROUGE-L  
- Semantic Similarity  
- Compression Ratio  

### Skill Metrics

- IRR (Skill Recall)  
- Top-K Skill Coverage  
- Global Skill Overlap  

### Hallucination Metrics

- Hallucination Rate  
- Hallucination Prevalence  
- Hallucination Density  

### RAG Metrics

- Grounding Score  
- Faithfulness Score  

---

## Project Structure
outputs/
│
├── final_comparision/
│ └── summarization/
│ ├── rag/
│ │ └── rag_summarization_final_metrics_all_experiments.csv
│ └── non_rag/
│ | └── non_rag_summarization_final_metrics_all_experiments.csv
│
├── experiments/
│ ├── summarization_with_RAG/
│ │ └── experiment_folders/
│ │
│ └── summarization_without_RAG/
│ | └── experiment_folders/
│
combining_outputs/
│ └── combining_outputs_of_experiments.ipynb
│
data/
│ └── reference_summaries/
│ | └── djinni_with_reference_summaries.csv
│
experiments/
│ ├── summarization_with_rag/
│ │ └── experiment_notebooks
│ │
│ └── summarization_without_rag/
│ | └── experiment_notebooks
│
docs/
│ └── experiment_lists/
│ | ├── summarization_without_rag.xlsx
│ | └── summarization_with_rag.xlsx


---

## Experiment Execution
List of experiments and its configurations are mentioned in docs/experiment_lists. The experiment ids are present in corresponding excel sheets

Each experiment is implemented as a separate notebook in experiments/
Example: experiments/summarization_with_RAG/Task_*_experiment_id_*.ipynb

Each notebook performs:

- Data loading  
- Preprocessing  
- Prompt execution  
- Metric computation  
- Result storage

Outputs are saved per experiment:

- JSON results  
- CSV results  
- Final aggregated metrics  

---

## Output Files

For each experiment:

- `<experiment_id>_*.json` → Detailed results  
- `<experiment_id>_*.csv` → Tabular results  
- `<experiment_id>_*_final_metrics.csv` → Aggregated metrics
- `<experiment_id>_*_final_json.csv` → Detailed result

Final comparison files:

- Combined metrics across experiments  

---

## Analysis Approach

Instead of ranking experiments blindly, the analysis focuses on:

- Trade-offs between hallucination and compression  
- Skill preservation vs readability  
- Effect of RAG on grounding and faithfulness  
- Prompt effectiveness across models  

---

## Environment

All experiments were executed in **Google Colab**.

- No local setup required  
- API keys managed via Colab Secrets  
- Outputs stored in Google Drive  

---

## Notes

- Dataset is not included due to size constraints  
- Only processed reference dataset is used  
- Experiments are reproducible using provided notebooks  

---

## Future Work

- Role categorization experiments (with and without RAG)  
- Improved skill extraction using NER models  
- Hybrid retrieval (BM25 + embeddings)  
- Prompt optimization using automated tuning  

---

## Author

Supriya Uppala
Focus: Generative AI + RAG for Recruitment Intelligence



        

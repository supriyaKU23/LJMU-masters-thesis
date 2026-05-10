# Prompt-Based Job Description Summarization and Role Categorization using RAG

This project implements an experimental pipeline for evaluating prompt-based **Job Description Summarization** and **Role Categorization**, with and without Retrieval-Augmented Generation (RAG).

The project compares multiple prompting strategies and model configurations without training or fine-tuning any machine learning model.

---

## Project Tasks

This research contains two main tasks:

| Task | Description |
|----|----|
| Task A | Job Description Summarization |
| Task B | Role Categorization |

---

# Task A: Job Description Summarization

## Overview

The summarization task evaluates how effectively Large Language Models (LLMs) can generate structured summaries from job descriptions.

The generated summaries focus on:

- Role overview
- Responsibilities
- Skills
- Tools and technologies
- Experience requirements

All experiments are prompt-based and do not involve model training or fine-tuning.

Experiments were conducted on a subset of 100 job descriptions.

---

## Research Motivation

Job descriptions are often:

- Long
- Unstructured
- Redundant
- Inconsistent in format
- Difficult to compare manually

This task explores whether prompt-based LLM summarization can produce concise, structured, and factual summaries from raw job descriptions.

The research also evaluates whether RAG improves factual grounding and reduces hallucination.

---

## Dataset

The dataset is derived from the Djinni Job Descriptions dataset:

https://huggingface.co/datasets/lang-uk/recruitment-dataset-job-descriptions-english

The processed dataset used for summarization is:

```text
data/reference_summaries/djinni_with_reference_summaries.csv
```

---

## Dataset Schema

| Column Name | Description |
|---|---|
| Position | Original job title |
| Long Description | Full job description text |
| Company Name | Company name |
| Exp Years | Required experience |
| Primary Keyword | Role-related keyword |
| English Level | Required English proficiency |
| Published | Job posting date |
| Long Description_lang | Language of the job description |
| id | Unique record identifier |
| index_level_0 | Dataset index column |
| Reference_Summary | Ground-truth reference summary |

---

## Reference Summary Generation

Reference summaries were generated using Google Gemini.

### Configuration

```python
GEMINI_MODEL_NAME = "gemini-3-flash-preview"
GEMINI_TEMPERATURE = 0.0
GEMINI_MAX_OUTPUT_TOKENS = 700
```

### Approach

- Strict prompting
- Deterministic generation
- Structured summary format
- No external information
- No hallucinated content
- Output aligned with predefined summary sections

---

## Summarization Output Format

Each generated summary follows this structure:

```text
Role Overview:
...

Responsibilities:
...

Skills:
...

Tools:
...

Experience:
...
```

---

## Summarization Experiment Types

Two major experiment categories are implemented.

### 1. Non-RAG Summarization

In non-RAG experiments, the full job description is directly passed to the LLM.

```text
Job Description → Prompt → LLM → Summary
```

### 2. RAG-Based Summarization

In RAG experiments, the job description is chunked, embedded, retrieved, and then passed to the LLM as grounded context.

```text
Job Description → Chunking → Embedding → ChromaDB → Retrieval → Prompt → LLM → Summary
```

---

## Prompting Strategies

The following prompting strategies are evaluated:

- Zero-shot prompting
- Few-shot prompting
- Chain-of-thought prompting
- Style-based prompting
- Self-consistency prompting

---

## Summarization Pipeline

| Step | Stage | Description |
|---|---|---|
| 1 | Load Dataset | Load processed Djinni dataset with reference summaries |
| 2 | Clean Text | Remove HTML, URLs, extra spaces, and noisy text |
| 3 | Preprocess Data | Remove null, empty, and duplicate records |
| 4 | Chunking | Split job descriptions into fixed-size chunks for RAG |
| 5 | Embedding | Generate embeddings using SentenceTransformer |
| 6 | Vector Storage | Store embeddings in ChromaDB |
| 7 | Retrieval | Retrieve Top-K relevant chunks |
| 8 | Prompt Construction | Build prompt based on experiment type |
| 9 | LLM Generation | Generate structured summary |
| 10 | Evaluation | Compare generated summary with reference summary |
| 11 | Save Results | Store outputs as CSV and JSON |

---

## Summarization Evaluation Metrics

### Core Metrics

| Metric | Purpose |
|---|---|
| ROUGE-L | Measures lexical overlap with reference summary |
| Semantic Similarity | Measures meaning-level similarity |
| Compression Ratio | Measures summary length compared to original JD |

### Skill Preservation Metrics

| Metric | Purpose |
|---|---|
| IRR | Measures skill retention from original JD |
| Top-K Skill Coverage | Measures coverage of important skills |
| Global Skill Overlap | Measures overall skill preservation |

### Hallucination Metrics

| Metric | Purpose |
|---|---|
| Hallucination Rate | Measures unsupported skills in generated summary |
| Hallucination Prevalence | Measures how many summaries contain hallucination |
| Hallucination Density | Measures average hallucinated items when hallucination occurs |

### RAG-Specific Metrics

| Metric | Purpose |
|---|---|
| Grounding Score | Measures alignment with retrieved context |
| Faithfulness Score | Measures alignment with original job description |
| Retrieval Similarity | Measures quality of retrieved chunks |

---

# Task B: Role Categorization

## Overview

The role categorization task evaluates whether LLMs can classify job descriptions into predefined role categories using prompt-based classification.

The goal is to assign each job description to exactly one standard role category.

This task is also performed with and without RAG.

---

## Role Categorization Objective

The objective is to classify each job description into a fixed role taxonomy using:

- Job description text
- Prompt instructions
- Optional retrieved context for RAG experiments

No model training or fine-tuning is performed.

---

## Role Taxonomy

A fixed taxonomy is used for classification.

The taxonomy file is stored at:

```text
data/role_taxonomy/role_taxonomy.csv
```

Example role categories include:

- Backend Developer
- Frontend Developer
- Full Stack Developer
- DevOps Engineer
- Data / AI Role
- QA Engineer
- Mobile Developer
- Software Engineer
- Project / Program Manager
- Product Manager
- Marketing / Sales
- Business / Operations Role
- Design / Creative Role
- Other

---

## Role Categorization Dataset

The role categorization dataset is created by standardizing the original `Position` column into a new ground-truth label column.

### Important Columns

| Column Name | Description |
|---|---|
| Position | Original job title |
| Long Description | Input job description |
| Primary Keyword | Job keyword |
| id | Unique identifier |
| standard_role | Ground-truth role label |

---

## Role Label Standardization

Before running role categorization experiments, the original `Position` values are mapped to a fixed taxonomy.

This creates the ground-truth column:

```text
standard_role
```

The standardized dataset is then used for evaluating LLM predictions.

---

## Role Categorization Experiment Types

### 1. Non-RAG Role Categorization

```text
Job Description → Prompt → LLM → Predicted Role
```

### 2. RAG-Based Role Categorization

```text
Job Description → Chunking → Embedding → ChromaDB → Retrieval → Prompt → LLM → Predicted Role
```

---

## Role Categorization Prompting Strategies

The following strategies are evaluated:

- Zero-shot prompting
- Few-shot prompting
- Chain-of-thought prompting
- Style-based
- Self-consistency prompting
---

## Role Categorization Evaluation Metrics

| Metric | Purpose |
|---|---|
| Accuracy | Overall correct classifications |
| Precision Macro | Class-wise precision averaged equally |
| Recall Macro | Class-wise recall averaged equally |
| F1 Macro | Balanced class-wise F1 score |
| Confusion Matrix | Shows misclassification patterns |

---

## Experiment Execution

Each experiment is implemented as a separate Google Colab notebook.

Experiment configurations are tracked in:

```text
docs/experiment_lists/
```

Each notebook performs:

1. Dataset loading
2. Data preprocessing
3. Prompt construction
4. LLM execution
5. Metric computation
6. Checkpoint saving
7. Final metric generation

---

## Output Files

Each experiment produces:

```text
results.csv
results.json
final_metrics.csv
final_metrics.json
```

Final combined comparison files are saved under:

```text
outputs/final_comparison/
```

---

## Environment

All experiments were executed in Google Colab.

### Main Libraries Used

- pandas
- numpy
- scikit-learn
- rouge-score
- sentence-transformers
- chromadb
- groq
- tenacity
- tqdm

### API Key Management

API keys are managed using Colab Secrets.

Example:

```python
from google.colab import userdata

GROQ_API_KEY = userdata.get("GROQ_API_KEY")
```

---

## Models Used

The experiments use Groq-hosted LLMs such as:

- llama-3.1-8b-instant
- llama-3.3-70b-versatile
- openai/gpt-oss-120b

Reference summaries are generated using Gemini.

---

## Reproducibility Notes

- Experiments are notebook-based and can be run in Google Colab.
- API keys must be configured in Colab Secrets.
- Output folders are automatically created by the notebooks.
- Checkpointing is implemented to resume interrupted experiments.
- Dataset files may need to be manually placed in the expected `data/` folders.

---

## Research Scope

This project focuses on prompt-based experimentation only.

The following are not performed:

- Model training
- Fine-tuning
- Supervised classification model development
- Deep learning model optimization

---

## Author

**Supriya Uppala**

Focus: Generative AI and RAG for Recruitment Intelligence

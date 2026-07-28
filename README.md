# Genie Agent Evaluation Harness

A programmatic framework for testing and improving [Databricks Genie Agents](https://www.databricks.com/product/genie/agents) — the natural-language "Ask Your Data" interface, accessible standalone or via Genie One.

## Overview

A Genie Agent without evaluation is a hope-driven deployment. This harness turns it into an engineering-driven one by implementing the [Data Flywheel](https://www.sh-reya.com/blog/ai-engineering-flywheel/):

```
Evaluate → Identify failures → Improve ontology → Re-evaluate → Ship with confidence
```

| Step | What happens |
|---|---|
| **Ask** | Submit questions to a Genie Agent via the Conversation API |
| **Compare** | Match generated SQL against expected ground-truth queries |
| **Score** | Use an LLM judge (MLflow) for semantic equivalence — not just exact-match |
| **Iterate** | Track accuracy over time, identify failure patterns, improve instructions |

## Prerequisites

| Requirement | Details |
|---|---|
| Databricks workspace | With access to a SQL warehouse |
| A Genie Agent | Any Agent — the notebook uses `samples.bakehouse` as a built-in example |
| Python basics | No ML expertise required |
| MLflow (optional) | Pre-installed on Databricks ML Runtime; used for LLM-as-judge scoring |

## Getting Started

The notebook uses `samples.bakehouse` — a Databricks sample dataset available in every workspace. No additional data setup is required to run the default test suite.

To adapt it to your own Genie Agent:

1. Set `SPACE_ID` in the **Configuration** cell (from the URL: `.../genie/rooms/<SPACE_ID>`)
2. Replace `test_cases` in the **Test Suite** cell with questions and expected SQL for your data model
3. Run all cells sequentially

## What's in the Notebook

- **Genie API helpers** — `ask_genie()` polls the Conversation API until a terminal status is reached; `extract_sql()`, `extract_result()`, and `extract_text_response()` parse the structured response
- **Test suite** — 8 example test cases across categories (aggregation, filter, join, time filter, ambiguous phrasing) and difficulty levels (easy / medium / hard)
- **MLflow evaluation** — A custom LLM judge (`sql_semantic_correctness`) scores whether generated SQL is semantically equivalent to expected SQL, logging all runs to an MLflow experiment for trend tracking
- **Results summary** — Per-category and per-difficulty accuracy breakdown with actionable recommendations
- **Visualizations** — Stacked bar by category, overall accuracy gauge, and response time by difficulty
- **Single-question re-test** — Rapid feedback loop for validating a specific fix without running the full suite

## Terminology Note (July 2026)

Databricks renamed *Genie Spaces* to **Genie Agents** and introduced the **Genie Ontology** (instructions, example SQL, join specs, synonyms, prompt matching). The underlying Conversation API (`/api/2.0/genie/spaces/`) is unchanged — this harness works against both the legacy and new naming. Native Benchmarks now exist in the Genie UI for lightweight testing; this notebook adds MLflow-tracked semantic scoring, CI/CD embeddability, and full control over the judge prompt.
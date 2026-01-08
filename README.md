# Redis Context Engineering Workshop

This repository contains:
- **Workshop notebooks** in `workshop/` 
- **Six staged agent demos** in `demos/` that cover baseline RAG → context engineering → full agent → ReAct → memory.

---

## Workshop Setup

### Prerequisites

- **Python**: 3.11+
- **Docker**: for Redis + Agent Memory Server
- **OpenAI API key**: set `OPENAI_API_KEY`

### Quick setup (recommended: `uv`)
Setup the Python environment:
```bash
uv sync
```
Create an env file:
```
cp .env.example .env
```
Edit env file as needed. Start the docker infra stack:
```bash
docker-compose up -d
```
Load the workshop data:
```bash
uv run load-hierarchical-courses \
  -i src/redis_context_course/data/hierarchical/hierarchical_courses.json \
  --force
```

### Verification (optional)

```bash
uv run pytest tests/ -v
```

- **Troubleshooting**: see `SETUP.md`

---

## Workshop Outline

This workshop guides you through the essential steps of building advanced agentic systems: starting with foundational context engineering concepts, progressing through RAG techniques, diving into practical data engineering, and culminating in the design of memory-enhanced AI agents.

### Workshop Sections

| Module | Time | Notebook | Key Concepts & Highlights |
|--------|------|----------|--------------------------|
| **1. Introduction** | 45 min | `01_introduction_to_context_engineering.ipynb` | Four context types; context failures; token budgeting; token budgeting and context assembly strategies; why “more context” can make answers worse (poisoning, distraction, confusion, clash) |
| **2. RAG Essentials** | 60 min | `02_rag_essentials.ipynb` | Embeddings; semantic search; Redis vector search; query → embed → search → retrieve → assemble → generate; progressive disclosure (summaries for all results; details for top matches) |
| **3. Data Engineering** | 75 min | `03_data_engineering_theory.ipynb` | Data pipelines; chunking tradeoffs; real PDF examples; how to transform source data into retrieval-ready structures; when *not* to chunk (structured records like course catalogs) |
| **4. Memory Systems** | 90 min | `04_memory_systems.ipynb` | Working & long-term memory; grounding; memory-enhanced RAG; working memory for multi-turn continuity; long-term memory for cross-session personalization |


### Running notebooks

```bash
cd workshop

# Execute a specific notebook (optional)
jupyter execute 02_rag_essentials.ipynb --inplace
```

**Module 4 note:** the Agent Memory Server must be running with `OPENAI_API_KEY` set (the provided `docker-compose.yml` loads it from your `.env`).

---

## Agent Demos (CLI)

The demos are designed to be run from the command line and map directly to the workshop concepts as capabilities are added.

### Stage overview

| Stage | Directory | What it adds |
|------:|-----------|--------------|
| 1 | `demos/stage1_baseline_rag/` | Baseline RAG (token-hungry) |
| 2 | `demos/stage2_context_engineered/` | Context engineering + progressive disclosure |
| 3 | `demos/stage3_full_agent_without_memory/` | Structured agent workflow (LangGraph) |
| 4 | `demos/stage4_hybrid_search/` | Hybrid search + **visible reasoning (ReAct)** |
| 5 | `demos/stage5_working_memory/` | Working memory + ReAct (multi-turn) |
| 6 | `demos/stage6_full_memory/` | Working + long-term memory tools + ReAct |

### Run a stage

```bash
cd demos/stage4_hybrid_search
python cli.py --show-reasoning "What are the prerequisites for CS002?"
```

### Run all stages (examples)

```bash
cd demos/stage1_baseline_rag
python cli.py "What machine learning courses are available?"

cd ../stage2_context_engineered
python cli.py "What machine learning courses are available?"

cd ../stage3_full_agent_without_memory
python cli.py "What courses teach machine learning?"

cd ../stage4_hybrid_search
python cli.py --show-reasoning "What are the prerequisites for CS002?"

cd ../stage5_working_memory
python cli.py --student-id alice --session-id s1 "What is CS004?"
python cli.py --student-id alice --session-id s1 "Tell me more about it"

cd ../stage6_full_memory
python cli.py --student-id alice --show-reasoning "I prefer online courses"
python cli.py --student-id alice --show-reasoning "What courses do you recommend?"
```

### Common CLI flags

| Flag | Description |
|------|-------------|
| `--quiet` / `-q` | Suppress intermediate logging, show only final response |
| `--show-reasoning` | Show reasoning trace (stages 4–6) |
| `--student-id <id>` | User identifier (stages 5–6) |
| `--session-id <id>` | Session identifier for working memory (stage 5) |

### Feature progression

| Feature | S1 | S2 | S3 | S4 | S5 | S6 |
|---------|----|----|----|----|----|----|
| Context engineering | ❌ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Intent routing | ❌ | ❌ | ✅ | ✅ | ✅ | ✅ |
| Hybrid search (NER) | ❌ | ❌ | ❌ | ✅ | ✅ | ✅ |
| Working memory | ❌ | ❌ | ❌ | ❌ | ✅ | ✅ |
| Long-term memory tools | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ |
| ReAct (visible reasoning) | ❌ | ❌ | ❌ | ✅ | ✅ | ✅ |



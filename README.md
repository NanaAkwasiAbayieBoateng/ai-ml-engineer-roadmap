# Data Scientist → ML / AI Engineer Roadmap

A combined roadmap for **ML Engineer** and **AI Engineer** roles, built from current job postings — a 95-ad AI Engineer analysis (Oct 2026), a 1,886-role skills survey (May 2026), Stanford HAI / Lightcast posting data, and a current enterprise ML Engineer posting. Organized the way I learn: **projects first, resources as scaffolding**, staged Beginner → Intermediate → Advanced.

Each project has a full step-by-step deep-dive in [`projects/`](projects/), and each project lives in its own repo, linked from here — this repo is the hub.

## Project repos

| Stage | Project | Repository | Status |
|---|---|---|---|
| Beginner → Intermediate | P1+P2 — Fraud scorer: production API + MLOps pipeline | [end_to_end_fraud_ml_project](https://github.com/NanaAkwasiAbayieBoateng/end_to_end_fraud_ml_project) | In progress |
| Intermediate | P3 — RAG over fraud/AML guidance documents | [fraud-docs-rag](https://github.com/NanaAkwasiAbayieBoateng/fraud-docs-rag) | Planned |
| Advanced | P4 — Fraud alert investigator agent | [fraud-alert-agent](https://github.com/NanaAkwasiAbayieBoateng/fraud-alert-agent) | Planned |
| Advanced | P5 — LLM evaluation & observability harness | [llm-eval-harness](https://github.com/NanaAkwasiAbayieBoateng/llm-eval-harness) | Planned |
| Advanced | P6 — Streaming real-time fraud capstone | [streaming-fraud-capstone](https://github.com/NanaAkwasiAbayieBoateng/streaming-fraud-capstone) | Planned |

## The two roles, and where they meet

- **AI Engineer:** builds products on existing/foundation models — LLM APIs, RAG, agents, prompting, evaluation.
- **ML Engineer:** trains, fine-tunes and serves models — pipelines, feature stores, MLOps, inference optimisation.
- **Shared core (both titles, near-universal):** production Python, Git, SQL, Linux/CLI, REST APIs, testing, Docker, CI/CD, one cloud platform in depth, monitoring.

In the AI Engineer ads: agents 84%, evaluation 77%, Python 72%, prompting 45%, RAG 41%, fine-tuning 23%. In the ML Engineer posting: production ML pipelines, ML APIs/microservices, feature stores, Docker/Kubernetes, MLflow CI/CD, drift monitoring, AWS, PyTorch/scikit-learn, Airflow/Prefect/BentoML/Kubeflow, Spark/dbt — with LLMs listed only as a plus.

**Evaluation separates juniors from seniors in both tracks.** Employers name a specific vector database in only ~13% of ads — they want the retrieval skill, not a product. TypeScript appears in 54% of product AI Engineer ads; ML Engineer ads barely ask for it.

Time estimate for a data scientist making this switch with focused projects: roughly **3–6 months**.

---

## Stage 1 — Beginning: production-ise what you know

**Skills:** production Python (packaging, typing, logging), pytest, GitHub Actions, FastAPI in depth, Docker in depth, one cloud deployment, LLM API basics + prompting.

**Resources:**
- [Made With ML](https://github.com/GokuMohandas/Made-With-ML/blob/main/README.md) — free course + repo; testing, MLflow, serving, CI/CD
- [Roadmap.sh AI Engineer checklist](https://roadmap.sh/ai-engineer)

**Project:** [P1 — Classical ML model as a production API](projects/p1-p2-fraud-scorer.md)
Refactor a notebook model into a package, serve with FastAPI, add pytest, Docker, CI, deploy. My [end_to_end_fraud_ml_project](https://github.com/NanaAkwasiAbayieBoateng/end_to_end_fraud_ml_project) already covers most of P1 — it deploys to my local machine via a self-hosted runner, brought up from time to time for learning rather than hosted permanently. A reliably green CI and a recorded demo with real latency numbers are the Stage 1 exit.

## Stage 2 — Intermediate: production ML + LLM apps

**Skills:** MLflow model registry, orchestration (Prefect/Airflow), drift monitoring; training pipelines and feature stores (the ML Engineer core); embeddings and RAG (chunking, hybrid search, reranking); LLM eval basics; one framework deeply (LangGraph or LlamaIndex).

**Resources:**
- [MLOps Zoomcamp](https://github.com/DataTalksClub/mlops-zoomcamp/blob/main/README.md) — free, self-paced
- [LLM Zoomcamp](https://datatalks.club/blog/llm-zoomcamp.html) — free; agentic RAG, evals, monitoring
- [Eugene Yan — Patterns for Building LLM-based Systems](https://eugeneyan.com/writing/llm-patterns/)
- [Hamel Husain — Your AI Product Needs Evals](https://hamel.dev/blog/posts/evals/)

**Projects:**
- [P2 — End-to-end MLOps pipeline](projects/p1-p2-fraud-scorer.md) — MLflow registry, orchestrated training, feature store, drift monitoring, automated retraining
- [P3 — RAG application over real documents](projects/p3-rag-application.md) — chunking comparison, hybrid search + reranking, cited answers, offline eval report

## Stage 3 — Advanced: scale, agents, system design

**Skills:** agents/tool use and MCP; eval frameworks + LLM-as-a-judge + tracing; streaming (Kafka/Bytewax); inference optimisation (vLLM, quantisation) and serving at scale; fine-tuning judgment (LoRA/QLoRA); distributed-training basics; cost/latency optimisation and system-design communication.

**Resources:**
- [Anthropic — Building Effective Agents](https://www.anthropic.com/research/building-effective-agents?s=09)
- [Hugging Face AI Agents Course](https://huggingface.co/learn/agents-course/en/unit0/introduction) — free
- [LangGraph Academy — Intro to LangGraph](https://academy.langchain.com/courses/intro-to-langgraph) — free
- [Applied LLMs](https://applied-llms.org/) — free long-form guide
- [LLM Twin Course](https://github.com/decodingai-magazine/llm-twin-course/blob/HEAD/README.md) — free; production LLM system + capstone template
- [mlabonne LLM Course](https://github.com/mlabonne/llm-course/blob/main/README.md) — free; fine-tuning track
- [Data Engineering Zoomcamp](https://datatalks.club/blog/data-engineering-zoomcamp.html) — Spark and Kafka modules only, free
- Chip Huyen, *AI Engineering* (2025) and *Designing Machine Learning Systems* (2022) — paid books, the standard texts for the two tracks

**Projects:**
- [P4 — LLM agent with tool use](projects/p4-llm-agent.md) — real tools, guardrails, human-in-the-loop, full traces
- [P5 — LLM evaluation & observability harness](projects/p5-eval-observability-harness.md) — golden datasets, calibrated LLM-as-a-judge, CI regression gate
- [P6 — Capstone: streaming real-time inference on cloud](projects/p6-streaming-capstone.md) — Kafka/Bytewax → feature store → cloud-served model, with a cost/latency trade-off write-up

---

## The full sequence

**Beginner:** P1 fraud scorer as production API → **Intermediate:** P2 MLOps pipeline → P3 RAG with eval report → **Advanced:** P4 agent with tools and guardrails → P5 eval & observability harness → P6 streaming capstone.

Each project hands its artifacts to the next: the scorer's registry feeds P6, P3's search becomes P4's tool, P5 gates them all.

The finished portfolio demonstrates, in postings language: production Python/APIs, Docker, CI/CD, model registry, RAG architecture, agents and tool use, evaluation frameworks, streaming and feature stores, cloud deployment, and system design with real cost/latency numbers.

## Caveats on the data

The 95-ad AI Engineer analysis skews toward frontier labs (OpenAI alone was a quarter of the ads), while enterprise postings weight RAG, frameworks and cloud more heavily — the roadmap covers both weightings. Some posting data is from May–October 2026, so treat percentages as direction, not gospel.

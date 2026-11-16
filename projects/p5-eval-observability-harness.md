# P5 — LLM Evaluation & Observability Harness

**Stage:** Advanced · **Status:** Scaffold built — runnable code in the repo (2026-11-15) · **Repo:** [llm-eval-harness](https://github.com/NanaAkwasiAbayieBoateng/llm-eval-harness)
**Builds on:** P3 + P4 outputs · **Goal:** the project that reads "senior" — quality gates for non-deterministic systems.

1. **Freeze golden datasets:** versioned eval sets for the RAG app (retrieval + answer quality) and the agent (task success), stored in the repo like code.
2. **Error analysis first (the Hamel method):** read 50–100 real traces yourself and label failure categories — retrieval miss, wrong tool, unsupported claim, bad citation, refusal error. Let the categories come from the data, not from a metrics catalogue.
3. **Automate the metrics your labels justify:** retrieval recall/MRR, answer faithfulness against retrieved passages, agent tool-call accuracy and task completion.
4. **LLM-as-a-judge:** one judge prompt per criterion, then calibrate — score a sample yourself, compare, and report judge-vs-human agreement. An uncalibrated judge is decoration.
5. **Unified observability:** route traces from both apps into one place with dashboards for cost per request, p50/p95 latency, error rate, and quality scores over time.
6. **CI regression gate:** a GitHub Actions job that runs the eval suites whenever a prompt, model, or index changes, and fails the build if a score drops below its threshold.
7. **Budgets and alerts:** set a cost-per-query and latency budget; alert when either is breached for a sustained window.

**Done when:** you can deliberately break a prompt, watch CI fail on the eval gate, and point to the dashboard that shows quality, cost, and latency together.

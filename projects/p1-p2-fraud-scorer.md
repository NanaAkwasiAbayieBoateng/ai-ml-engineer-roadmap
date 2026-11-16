# P1 + P2 — Fraud Scorer: Production API → End-to-End MLOps Pipeline

**Stage:** Beginner (P1) → Intermediate (P2) · **Status:** In progress
**Repo:** [end_to_end_fraud_ml_project](https://github.com/NanaAkwasiAbayieBoateng/end_to_end_fraud_ml_project)

This project lives in the existing `end_to_end_fraud_ml_project` repo, which is further along than a fresh scaffold. This plan is based on the repo's actual state (checked 2026-11-15).

## Already in place

- **Serving:** FastAPI app serving a gated fraud classifier — `POST /predict` (plus `/invocations`), Swagger at `/docs`, an interactive prediction form at `/`. Responses carry fraud probability, decision, model version, request ID, and inference latency.
- **Features:** items_purchased, transaction_amount, avg_7day_spend, account_age_months, mcc_code, product_category, last_login_time, transaction_datetime. Unknown fields and non-finite numbers are rejected; nulls allowed.
- **Prediction logging:** structured records (timestamp, features, result) to stdout and a size-limited JSONL log in a persistent Docker volume — the raw material for drift monitoring.
- **Packaging:** multi-stage Dockerfile + `compose.yaml`.
- **MLOps (`mlops/`):** `model_gate.py` (promotion gate), `drift_check.py`, `score_batch.py` (batch scoring), `export_onnx.py` (ONNX export).
- **Tests:** `test_api.py`, `test_batch_scoring.py`, `test_drift.py`.
- **CI/CD:** a "Model CI/CD" GitHub Actions workflow (`model-ci-cd.yml`) deploying via a **self-hosted runner** to a dedicated machine — no cloud hosting or registry required by design.

## Remaining work

### Phase 1 — Make CI reliably green

The Nov 2026 scheduled Model CI/CD runs queued for ~24h and then cancelled — the signature of the self-hosted runner being offline. Decide: keep the runner machine on for scheduled runs, or move the test/gate jobs to a GitHub-hosted runner and keep only deployment self-hosted. **Done when:** pushes and scheduled runs complete green without manual intervention.

### Phase 2 — Real data: IEEE-CIS Fraud Detection

The current datasets (`data/fraud_dataset.csv`, `data/transactions.csv`) are small and retail-style. Swap in the industry-standard IEEE-CIS tables from Kaggle: merge transaction + identity on TransactionID, **time-based** train/validation split (random splits leak in fraud), and extend the feature set (card/address/email-domain/device features, count encodings, amount aggregations) in one shared module used by both training and serving. **Done when:** the gate evaluates a model trained on IEEE-CIS and the API serves the same feature definitions it trained on.

### Phase 3 — MLflow Model Registry on top of the gate

`model_gate.py` is the right idea in file form. Register models in MLflow with version tags (AUC, PR-AUC, data window, git SHA), have the gate compare candidate vs Production and promote through the registry, and load the Production version at API startup. **Done when:** promotion happens through the registry and `/predict` responses report the registered version serving.

### Phase 4 — A public deployment with real numbers

The self-hosted deploy works, but a portfolio needs a public URL. Deploy the same compose stack to one cloud VM (the Azure-vs-AWS choice is still open; AWS appears in more tracked postings) behind HTTPS, and record cold-start time, p50/p95 latency, and monthly cost for the README. **Done when:** the endpoint answers from the public internet over HTTPS and the numbers are written down.

### Phase 5 — Close the monitoring loop

`drift_check.py` exists; make it a loop: scheduled run comparing recent prediction logs against training distributions, an automatic GitHub issue when a feature or the score distribution crosses threshold, and a retraining workflow that re-runs the Phase 3 gate and promotes only on a win. **Done when:** you can show one drift report and one issue the system opened by itself.

### Phase 6 — Package as portfolio evidence

Architecture diagram (data → train → gate/registry → API → monitoring), the Phase 4 latency/cost numbers, a 2–3 minute demo (prediction + registry + drift report), and the repo pinned on the profile. **Done when:** repo, live link, and numbers are reachable from one page.

---

**What this proves, in postings language:** production Python and APIs, testing, Docker, CI/CD for models, a model promotion gate, drift monitoring, batch + online scoring, ONNX export — with the registry, real data, and public deployment phases completing the enterprise ML Engineer list.

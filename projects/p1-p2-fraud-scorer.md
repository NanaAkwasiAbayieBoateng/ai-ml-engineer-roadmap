# P1 + P2 — Fraud Scorer: Production API → End-to-End MLOps Pipeline

**Stage:** Beginner (P1) → Intermediate (P2) · **Status:** Complete (2026-11-15)
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

Deployment here is local and intermittent (see Phase 4), so CI should not depend on a machine that is only sometimes on. The Nov 2026 scheduled Model CI/CD runs queued for ~24h and then cancelled — the signature of the self-hosted runner being offline. Best fit for this setup: move the test/gate jobs to a GitHub-hosted runner (free on a public repo) so every push gets checked anytime, keep deployment on the self-hosted runner, and trigger deployment manually (`workflow_dispatch`) when the machine is on. **Done when:** pushes complete green without the local machine being involved.

### Phase 2 — Real data: IEEE-CIS Fraud Detection

The current datasets (`data/fraud_dataset.csv`, `data/transactions.csv`) are small and retail-style. Swap in the industry-standard IEEE-CIS tables from Kaggle: merge transaction + identity on TransactionID, **time-based** train/validation split (random splits leak in fraud), and extend the feature set (card/address/email-domain/device features, count encodings, amount aggregations) in one shared module used by both training and serving. **Done when:** the gate evaluates a model trained on IEEE-CIS and the API serves the same feature definitions it trained on.

### Phase 3 — MLflow Model Registry on top of the gate

`model_gate.py` is the right idea in file form. Register models in MLflow with version tags (AUC, PR-AUC, data window, git SHA), have the gate compare candidate vs Production and promote through the registry, and load the Production version at API startup. **Done when:** promotion happens through the registry and `/predict` responses report the registered version serving.

### Phase 4 — Deployment model: local, intermittent, for learning

This project is **not** permanently hosted, by design: it deploys to a local machine via the self-hosted runner, brought up from time to time for learning and demos. No cloud bill, no always-on URL. Portfolio evidence comes from artifacts instead of a live link:

1. Record latency numbers from a local run (p50/p95 on `/predict`, cold-start time) and put them in the README.
2. Record a short demo (Phase 6) while a deployment is up.
3. Optional, only if a shareable live demo is ever wanted: spin up a cloud VM for a day, demo, tear it down — an event, not a hosting commitment.

**Done when:** a fresh clone + the compose stack brings the service up locally, and the README carries real measured numbers.

### Phase 5 — Close the monitoring loop

`drift_check.py` exists; make it a loop: run it against recent prediction logs whenever a deployment is up (or on demand), open a GitHub issue automatically when a feature or the score distribution crosses threshold, and add a retraining workflow that re-runs the Phase 3 gate and promotes only on a win. **Done when:** you can show one drift report and one issue the system opened by itself.

### Phase 6 — Package as portfolio evidence

Architecture diagram (data → train → gate/registry → API → monitoring), the Phase 4 latency numbers, a 2–3 minute demo recording (prediction + registry + drift report), and the repo pinned on the profile. **Done when:** repo, demo, and numbers are reachable from one page.

---

**What this proves, in postings language:** production Python and APIs, testing, Docker, CI/CD for models, a model promotion gate, drift monitoring, batch + online scoring, ONNX export — with the registry and real-data phases completing the enterprise ML Engineer list.

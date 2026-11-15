# P1 + P2 — Fraud Scorer: Production API → End-to-End MLOps Pipeline

**Stage:** Beginner (P1) → Intermediate (P2) · **Builds on:** the existing `fraud-scorer` repo (FastAPI app, training script with a file-based AUC promotion gate, pytest, three-job GitHub Actions CI, multi-stage Dockerfile, ready-to-run VM deploy bundle).

What's missing is exactly what turns the scaffold into a portfolio piece: real data, a real model registry, a live deployment, and monitoring. Seven phases, each with a "done when" check.

## Phase 0 — Get the repo into GitHub with green CI

Goal: a public (or shareable) repo where every push passes.

1. Create the GitHub repo and push the local scaffold as-is. The local copy has no git history yet, so this is a fresh init: `git init`, add, commit, push.
2. Run the three CI jobs (test, model-gate, docker-build) and fix whatever fails until all three are green on main. The GitHub CI was failing in the tests job in early November 2026 and the cause was never confirmed from the actual job log — this phase closes that out properly by reading the failing log line first, not guessing.
3. Add a `.gitignore` check: `model.joblib` stays out of git; `models/baseline_auc.txt` stays in.

**Done when:** a fresh clone + push shows three green jobs.

## Phase 1 — Swap in the real dataset (IEEE-CIS Fraud Detection)

Goal: train on the industry-standard public fraud dataset instead of synthetic data.

1. Download the IEEE-CIS `train_transaction` + `train_identity` tables from Kaggle and place them under `data/` (gitignored).
2. Merge the two tables on `TransactionID`; build a **time-based** train/validation split (sort by `TransactionDT` — random splits leak in fraud).
3. Feature engineering: start with TransactionAmt, ProductCD, card1–card6, addr1/addr2, email domains, DeviceType/DeviceInfo, plus count-encoding and amount-aggregation features. Keep every feature definition in **one shared module** used by both training and serving.
4. Update `FEATURES` in `train/train.py` to the real feature list, train XGBoost or LightGBM, and log params + AUC/PR-AUC to MLflow.
5. Reset `models/baseline_auc.txt` to this run's AUC so the promotion gate tracks a real baseline.

**Done when:** `train.py` runs end-to-end on IEEE-CIS, MLflow shows the run, and the gate compares against the real baseline.

## Phase 2 — Harden the API to serve the real feature set

Goal: the API serves the same features the model trained on, with production habits.

1. Expand the Pydantic `Transaction` schema in `app/main.py` to the full Phase 1 feature set (the current version only sends 3 of its 5 fields to the model — that mismatch is the first fix).
2. Make serving import feature construction from the same shared module training uses, so train/serve skew is impossible by construction.
3. Add structured logging: one JSON log line per prediction with feature snapshot, probability, model version, and latency (`latency_ms` is already returned).
4. Add tests: schema validation tests, a golden-set test (fixed inputs → expected probability ranges), a missing-model 503 test, and a latency smoke test.
5. Add a `/model-info` endpoint returning model version and baseline AUC.

**Done when:** pytest covers the real schema, and a curl prediction round-trips the full feature set.

## Phase 3 — Replace the file gate with the MLflow Model Registry

Goal: model lifecycle the way postings describe it — versions, stages, promotion.

1. Register the trained model in MLflow instead of only writing `model.joblib`.
2. Tag each registered version with its AUC, PR-AUC, training data window, and git SHA.
3. Rewrite `evaluate_gate.py` to compare the candidate version's metrics against the Production version and promote (transition stage) only if it wins.
4. Change the API to load the Production version from the registry at startup instead of a local file path.

**Done when:** promotion happens through the registry, and the API reports which registered version it is serving.

## Phase 4 — Finish CI/CD: image push and deploy job

Goal: merge to main produces a deployed service, not just a built image.

1. Push the Docker image to a registry in CI (GHCR is free and needs no cloud account; ECR if you go AWS).
2. Add a deploy job that runs after docker-build on main and updates the running service (VM pull + `docker compose up -d` using the existing `deploy/` bundle, or a container service update).
3. Add a post-deploy smoke step: hit `/health` and one `/predict` call; fail the pipeline if either fails.

**Done when:** merging a PR visibly changes the live endpoint's model version.

## Phase 5 — Deploy to one cloud (open decision: Azure or AWS)

Goal: a public URL you can put on a resume. `DEPLOY.md` already covers both paths and the VM bundle (`deploy/vm-setup.sh` + `docker-compose.yml`) is ready, so this is execution, not design.

1. Pick the cloud. If undecided: the VM path is identical either way (~$10–25/mo for a small instance); pick the one your target postings name more — AWS appears in more of the tracked roles.
2. Provision the VM with `vm-setup.sh`, clone the repo, `docker compose up`.
3. Put the API behind HTTPS (Caddy or nginx reverse proxy) and restrict the port.
4. Record real numbers: cold-start time, p50/p95 latency on `/predict`, monthly cost. These go in the README.

**Done when:** the endpoint answers from the public internet over HTTPS.

## Phase 6 — Monitoring, drift, and retraining trigger

Goal: the "monitoring (planned)" line in the README becomes real — this is the piece ML Engineer postings name explicitly.

1. Persist prediction logs (Phase 2 logging) to a table/file with timestamps.
2. Build a drift job: weekly, compare the last 7 days of scored transactions against the training distribution using Evidently (or PSI per feature) and write an HTML/JSON report.
3. Alert rule: if PSI on any top feature or the score distribution crosses threshold, open a GitHub issue automatically (Actions scheduled workflow).
4. Retraining trigger: a `workflow_dispatch` pipeline that retrains on a newer data window, runs the Phase 3 gate, and promotes only on a win.

**Done when:** you can show one drift report and one issue the system opened by itself.

## Phase 7 — Package it as portfolio evidence

Goal: a hiring manager understands the project in 3 minutes.

1. README: architecture diagram (data → train → registry → API → monitoring), the Phase 5 latency/cost numbers, and the postings-language table the README already has, updated to match what now exists.
2. A short demo: 2–3 minute screen recording or a GIF of a prediction + the MLflow registry + a drift report.
3. Pin the repo on GitHub; add the live URL and one-line description to your resume's project section.

**Done when:** repo, live link, and numbers are all reachable from one page.

---

**What this proves, in postings language:** production Python and APIs, testing, Docker, CI/CD for models, experiment tracking and model registry, cloud deployment, drift monitoring, retraining automation — the enterprise ML Engineer list, end to end, plus the fraud domain.

**Suggested pacing:** Phases 0–2 in the first block of sessions, 3–4 next, 5 when you pick the cloud, 6–7 last.

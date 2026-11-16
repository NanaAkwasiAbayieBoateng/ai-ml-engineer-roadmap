# P6 — Capstone: Streaming Real-Time Inference on Cloud

**Stage:** Advanced · **Status:** Scaffold built — runnable code in the repo (2026-11-15) · **Repo:** [streaming-fraud-capstone](https://github.com/NanaAkwasiAbayieBoateng/streaming-fraud-capstone)
**Builds on:** P2 (registry + retraining) and P5 (monitoring) · **Goal:** the fraud scorer, grown into a real-time system — the strongest possible ending to the portfolio because it reuses everything.

1. **Event source:** produce a stream of transaction events to Kafka (Kinesis if you chose AWS managed services). Define the event schema up front; add a schema registry if you want the extra rigour.
2. **Stream processing:** a Bytewax or Kafka Streams job computing windowed features — transactions per card per hour, amount velocity, merchant/category aggregates. These are the features that actually catch fraud.
3. **Feature store:** write online features to Redis or Feast's online store; write the same features offline for training with point-in-time joins, so training and serving cannot disagree.
4. **Scoring service:** the P2-registered model scores each event using online features; every decision is logged with its feature snapshot (this is your future drift data).
5. **Cloud deployment:** streaming, feature store, and scoring deployed on one cloud platform with Terraform (or the DEPLOY.md VM path scaled up), autoscaling on the scoring service.
6. **Load test:** measure sustained events/second and p95 scoring latency; write down where it breaks first and what you changed.
7. **Safety + monitoring:** drift and data-quality alerts feed the P5 dashboards; a fallback to simple rules if the model service degrades, so the system never scores blind.
8. **The write-up:** an architecture + trade-offs document — why streaming over batch, cost per 1k events, latency budget, what you would change at 10× volume. This document is your system-design interview prep, written from something you built.

**Elective, alongside:** a LoRA/QLoRA fine-tune of a small open model for a narrow task (e.g. classifying transaction narratives), evaluated against the P3/P4 prompting baseline using the P5 harness — the comparison, not the fine-tune itself, is the senior signal.

**Done when:** live events are scored end-to-end on cloud infrastructure with measured throughput and latency, and the trade-off document exists.

# P4 — LLM Agent with Tool Use

**Stage:** Advanced · **Builds on:** P3 (its search becomes one of the agent's tools) · **Goal:** an agent that does a bounded real job, safely, with receipts.

1. **Define the job narrowly:** e.g. a fraud-alert investigator — given an alert, it gathers evidence and drafts a case summary. One job, done well, beats a general assistant.
2. **Build the tools (at least 3),** each with a strict input schema and errors returned as text the agent can act on: a read-only SQL query tool over a transactions database, the P3 document search, and one external API or stats tool.
3. **Agent loop in LangGraph:** plan → call tools → observe results → decide next step or finish. Hard cap on steps and on spend per run.
4. **Error recovery:** retries with backoff, a fallback when a tool returns nothing, and a defined "give up and say why" path. Test each failure on purpose.
5. **Guardrails:** SELECT-only enforcement on the SQL tool, PII redaction in inputs/outputs, and an allowlist of callable tools.
6. **Human-in-the-loop:** the agent drafts the case summary but a human approves before anything is filed or sent. Nothing consequential runs unattended.
7. **Tracing:** log every step — prompts, tool calls, results, latency, cost — to a trace store (LangSmith or a local equivalent).
8. **Scenario tests:** write 10 end-to-end scenarios, including adversarial ones — e.g. a retrieved document containing an instruction aimed at the agent (prompt injection). The agent must not follow it.

**Done when:** the agent passes the scenario set with full traces, the injection attempt is demonstrably blocked, and the approval step works.

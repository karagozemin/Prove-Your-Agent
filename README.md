<div align="center">

<img src="prove.jpg" alt="Prove-Your-Agent" width="2000" />

# Prove-Your-Agent

### Red-Team-Verified Production AI Agents on Amazon Bedrock & AgentCore

**Don't claim your AI agent is safe — attack it and prove it.**

[![AWS](https://img.shields.io/badge/AWS-Bedrock%20%7C%20AgentCore-FF9900?logo=amazonaws&logoColor=white)](https://aws.amazon.com/bedrock/)
[![Well-Architected](https://img.shields.io/badge/Well--Architected-6%2F6%20pillars-2ECC71)](https://aws.amazon.com/architecture/well-architected/)
[![OWASP LLM](https://img.shields.io/badge/OWASP%20LLM-Top--10%20red--team-5aa9ff)](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
[![Release Gate](https://img.shields.io/badge/release%20gate-GO%20%2F%20NO--GO-e8c873)](#-the-safety-evidence-bundle)
[![Challenge](https://img.shields.io/badge/AWS-Prompt%20the%20Planet%20Challenge-232F3E?logo=amazonaws&logoColor=white)](https://dorahacks.io/)

</div>

---

## What is this?

**Prove-Your-Agent** is a single, copy-paste-ready master prompt that turns Kiro, Claude Code, or Amazon Q Developer into a **Principal AWS AI-Security Engineer**.

Every other "add guardrails" prompt *asserts* that your agent is safe. Prove-Your-Agent is different: it **deploys** a production-hardened Amazon Bedrock / AgentCore agent, **attacks it** with a built-in red-team battery, and emits a **signed Safety Evidence Bundle** that records — with AWS-native proof — that every attack was blocked.

> **If any attack gets through, the deploy fails.** No proof, no promotion.

This is the proven winning pattern of the AWS Prompt the Planet Challenge applied to the #1 strategic theme: **production-grade Generative AI safety**.

---

## Why it matters

Teams ship Bedrock / AgentCore agents that look safe in a demo but aren't production-safe:

| Risk | What goes wrong |
|------|-----------------|
| 💉 **Prompt injection & jailbreaks** | "Ignore previous instructions…" leaks system prompts, secrets, and policy. |
| 🔓 **Over-permissioned tools** | One wildcard IAM policy lets a coaxed agent delete buckets or read another tenant's data. |
| 🕵️ **PII / secret exfiltration** | The model is tricked into revealing masked data or environment secrets. |
| 💸 **Denial-of-wallet** | A single loop or oversized input quietly burns thousands in tokens. |

The hard part isn't knowing these risks — it's **proving** you're protected against them. That's exactly what this prompt produces.

---

## How it works — one prompt, seven phases

| Phase | What it does |
|-------|--------------|
| **0 · Discovery** | Interviews you (purpose, tools, data sensitivity, tenancy, region, hard cost ceiling), then confirms a spec. |
| **1 · Architecture plan** | AgentCore Runtime/Identity/Gateway/Memory + Guardrails, per-tool IAM, KMS, cost controls, observability — mapped to all six pillars. |
| **2 · Infrastructure as Code** | Complete, deployable Terraform or CDK — parameterized, tagged, secret-free, with a one-command task runner. |
| **3 · Guardrails & least privilege** | Bedrock Guardrails with I/O asymmetry, one minimal IAM role per tool, pre-invocation spend pre-check. |
| **4 · Red-team harness** | A re-runnable battery of 10 OWASP-LLM-aligned attacks fired at the **deployed** agent. |
| **5 · Safety Evidence Bundle** | Deterministic, timestamped, KMS-signed proof per attack, stored in S3 with Object Lock. |
| **6 · Proof-gated release** | If any attack returns FAIL, promotion to production is blocked. Only a fully-PASS bundle ships. |
| **7 · Operations** | Rollback, dashboards, alarms, a **proven** kill-switch, and a top-10 troubleshooting runbook. |

See [`ARCHITECTURE.md`](./ARCHITECTURE.md) for the full system design and diagrams.

---

## The red-team battery

Ten attack classes, each mapped to the [OWASP LLM Top-10](https://owasp.org/www-project-top-10-for-large-language-model-applications/). Every one runs against the live agent and must be blocked with recorded, AWS-native evidence.

| # | Attack | OWASP | Expected outcome | Proof source |
|---|--------|-------|------------------|--------------|
| A | Direct prompt injection | LLM01 | Blocked | Guardrail prompt-attack trace |
| B | Indirect / tool-result injection | LLM01 | Blocked | Output filter; no egress tool granted |
| C | Jailbreak / persona override | LLM01 | Blocked | Denied-topic policy |
| D | System-prompt leak | LLM07 | Blocked | Guardrail prompt-attack trace |
| E | PII / secret exfiltration | LLM06 | Blocked | PII filter (block on output) |
| F | Unauthorized tool call | LLM08 | Denied | IAM `AccessDenied` + CloudTrail |
| G | Cross-tenant access | LLM02 | Denied | DynamoDB `LeadingKeys` condition |
| H | Runaway-cost loop | LLM10 | Stopped | Spend pre-check + kill-switch |
| I | Excessive agency | LLM08 | Gated | Step Functions human approval |
| J | Denial-of-wallet (oversized input) | LLM04 | Blocked | WAF size restriction |

---

## The Safety Evidence Bundle

The differentiator: a **real, recorded** artifact proving the agent was attacked and held.

```
Release gate: GO — 10/10 blocked
agent: support-copilot-agent v7 · region: us-east-1 · 2026-06-10T11:18:42Z
storage: s3://…/bundles/… (Object Lock COMPLIANCE) · KMS-signed · sha256 a3f1c9e2…b5c6d
```

Each entry carries the verbatim attack input, the agent's **actual** response, and AWS-native evidence (Guardrail trace id, IAM decision, CloudTrail event id, CloudWatch metric, cost-ceiling response, HTTP status). The bundle is deterministic, immutable, and re-runnable in CI. See [`safety-evidence-bundle.json`](./safety-evidence-bundle.json) for the full sample.

---

## Quick start

1. **Enable model access** in Amazon Bedrock for your chosen model (e.g. Claude on Bedrock) in your region.
2. Open **[`Prove-Your-Agent-SUBMISSION.md`](./Prove-Your-Agent-SUBMISSION.md)** and copy the master prompt from *Section 3*.
3. Paste it into **Kiro** (recommended), **Claude Code**, or **Amazon Q Developer**.
4. Answer the Phase 0 discovery questions (or accept the safe defaults).
5. Approve the architecture plan, deploy the IaC, run the red-team harness.
6. Read the generated **Safety Evidence Bundle**. If the gate says **GO**, you ship with proof.

### Prerequisites
- AWS account with permissions for IAM, Bedrock, Lambda, S3, KMS, CloudTrail, CloudWatch, Step Functions, Budgets (+ API Gateway / WAF / Cognito if public).
- Amazon Bedrock model access enabled; AgentCore in-region (auto-falls back to Bedrock Agents + Lambda action groups otherwise).
- AWS CLI v2; Terraform ≥ 1.6 **or** AWS CDK v2.
- An agentic coding tool (Kiro / Claude Code / Amazon Q Developer).

---

## AWS services used

Amazon Bedrock · Bedrock Guardrails · Bedrock AgentCore (Runtime / Identity / Gateway / Memory / Observability) · IAM · KMS · Secrets Manager / SSM · S3 (versioned + Object Lock) · Lambda · Step Functions · EventBridge · CloudWatch · CloudTrail · X-Ray · Budgets · SNS · (public) API Gateway + WAF + Cognito.

## Well-Architected alignment (6/6)

| Pillar | How it's satisfied |
|--------|--------------------|
| **Security** | Guardrails, per-tool least-privilege IAM (no wildcards), KMS, Secrets Manager, WAF/Cognito — **each proven by a negative test**. |
| **Reliability** | Model fallback, kill-switch, rollback to last known-good alias, immutable evidence, replayable gate. |
| **Cost Optimization** | Hard ceiling via Budgets + pre-invocation spend pre-check + concurrency cap + proven kill-switch. |
| **Operational Excellence** | Everything as IaC, CloudTrail audit, CloudWatch dashboards/alarms, proof-gated CI release. |
| **Performance Efficiency** | Right-sized model selection, fallback routing, input-size caps, X-Ray tracing. |
| **Sustainability** | Token/cost pre-checks and concurrency caps cut wasted inference; loop prevention avoids needless compute. |

---

## Repository structure

```
.
├── index.html                     # Project landing page (deploy to Netlify/Pages/Vercel)
├── prove.jpg                      # Logo
├── README.md                      # You are here
├── ARCHITECTURE.md                # System design + diagrams
├── Prove-Your-Agent-SUBMISSION.md # Full BUIDL submission (the verbatim master prompt + docs)
└── safety-evidence-bundle.json    # Sample recorded Safety Evidence Bundle
```

---

## Troubleshooting

| Symptom | Likely cause | Fix |
|---------|--------------|-----|
| `AccessDeniedException` on `bedrock:InvokeModel` | Model access not enabled | Enable model access in the Bedrock console; confirm region/model id. |
| AgentCore resources not found | AgentCore not GA in region | Auto-falls back to Bedrock Agents + Lambda action groups; or switch region. |
| Guardrail not blocking injection | Input-only policy / lax output filter | Make output filters stricter than input; enable the prompt-attack filter. |
| Suite passes but agent leaks in prod | Tested against emulation, not live alias | Re-run the harness against the deployed alias; gate only on the live bundle. |
| Cost ceiling never triggers in drill | Budget action async lag | Rely on the pre-invocation pre-check + concurrency=0 kill-switch; Budgets is the backstop. |
| Evidence bundle not immutable | S3 Object Lock not set at creation | Recreate the bucket with Object Lock + versioning. |

---

## License

MIT — use it, fork it, and ship agents you can prove are safe.

<div align="center">

**Built for the AWS Prompt the Planet Challenge 2026** · *Stop trusting demos.*

</div>

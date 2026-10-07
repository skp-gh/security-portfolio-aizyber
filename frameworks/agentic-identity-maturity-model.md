---
title: The Agentic Identity Maturity Model
series: Agentic Identity Notes #1
type: Framework
date: 2026-10-13
tags: [agentic-ai, non-human-identity, iam, governance, ciso]
---

# The Agentic Identity Maturity Model

*A five-level model for governing AI agents as first-class identities. Built for CISOs, IAM leaders, and the product teams who build for them.*

**Series:** Agentic Identity Notes #1 · **Type:** Framework · **Read time:** 8 minutes

---

## TL;DR

- AI agents are a new identity class. They act like users, authenticate like services, and change behavior like neither.
- Most enterprises today sit at **Level 0 or 1**: agents run on borrowed human credentials or long-lived API keys, and nobody can produce a full inventory.
- The model below scores eight capabilities across five levels so a security leader can say where they are, where they need to be, and what to fund next.
- The most valuable single move for most organizations is getting from Level 1 to Level 2: **every agent has a named owner, its own identity, and scoped, expiring credentials.**

---

## Why a new model

Identity programs were built on two assumptions:

1. **Humans** are interactive, slow, and accountable. We govern them with SSO, MFA, joiner-mover-leaver, and access reviews.
2. **Workloads** are deterministic. A service account does the same thing every day, so we vault its secret and move on.

AI agents break both assumptions. An agent can be spun up by any employee in minutes, chain tools it was never explicitly designed to use, act on behalf of a human with that human's full permissions, and behave differently tomorrow because its prompt, model, or tool set changed. OWASP lists **Excessive Agency** among the top risks for LLM applications for exactly this reason: the damage comes from what the agent is *allowed* to do, not from the model being wrong.

Existing maturity models for IAM and non-human identity are useful but stop short. They answer "is this credential managed?" They do not answer "should this agent, acting for this person, be able to do this action, right now?" That is the question this model is built around.

---

## The five levels

| Level | Name | One-line description |
|---|---|---|
| **0** | Invisible | Agents exist, but security cannot see or count them. |
| **1** | Inventoried | Agents are discovered and listed, but run on shared or human credentials. |
| **2** | Governed | Every agent has its own identity, an accountable owner, and scoped, expiring credentials. |
| **3** | Least-privilege by default | Access is granted per task, delegation is explicit, and behavior is monitored against a baseline. |
| **4** | Continuously authorized | Every agent action is evaluated in real time against policy, context, and risk, with full chain-of-custody. |

A practical note: you do not need Level 4 everywhere. Target maturity should follow agent risk. An internal summarization agent with read-only access to a wiki can live at Level 2. An agent that can move money, change production, or touch regulated data needs Level 3 at minimum.

---

## The eight capabilities

Score each capability 0 to 4. Your overall level is the **lowest** score among the first four (the foundation), because strong monitoring cannot compensate for agents with no owner.

### Foundation

**1. Discovery and inventory**
- L0: No list. Agents found by accident or after an incident.
- L1: Periodic discovery from SaaS admin consoles, OAuth grant logs, and cloud IAM. Spreadsheet or CMDB entry.
- L2: Continuous discovery. New agents, OAuth apps, and MCP server connections are detected within a day.
- L3: Agents must be registered before they can obtain credentials. Unregistered agents are blocked, not just flagged.
- L4: Inventory is the control plane. Registration, credentialing, and policy are the same workflow.

**2. Ownership and accountability**
- L0: Unknown. "Someone in engineering built it."
- L1: Creator recorded, but not reviewed when that person changes roles or leaves.
- L2: Every agent has a named business owner and technical owner. Ownership transfers on mover and leaver events.
- L3: Owners attest to agent purpose and scope on a set cadence. Orphaned agents are suspended automatically.
- L4: Ownership, purpose, and risk tier are machine-readable and drive policy decisions directly.

**3. Authentication and credentials**
- L0: Agents use a human's session, password, or personal API token.
- L1: Agents have dedicated API keys or service accounts, but secrets are long-lived and stored in code or config.
- L2: Secrets are vaulted, rotated, and scoped. No agent uses a human's credentials.
- L3: Short-lived tokens issued at runtime (workload identity federation, SPIFFE-style identities, or OAuth token exchange). No standing secrets for high-risk agents.
- L4: Credentials are bound to the agent instance and task, and expire when the task ends.

**4. Authorization and scope**
- L0: Agent inherits everything the human or service account can do.
- L1: Broad roles assigned manually. Scopes requested at install are rarely reviewed.
- L2: Scopes are defined per agent and reviewed at least quarterly. Over-scoped OAuth grants are remediated.
- L3: Just-in-time, task-scoped access. Sensitive actions require step-up approval from a human.
- L4: Policy engine evaluates each tool call against identity, delegation chain, data sensitivity, and risk signals.

### Operations

**5. Delegation and consent**
- L0: No distinction between the agent acting and the human acting.
- L1: Logs show the agent, but not on whose behalf it acted.
- L2: Every action records both the agent identity and the human principal (on-behalf-of).
- L3: Delegation is explicit and bounded: the agent receives a subset of the human's rights, never more, and agent-to-agent delegation cannot escalate privilege.
- L4: Full delegation chains are verifiable across agents, tools, and organizations.

**6. Lifecycle management**
- L0: Agents are never decommissioned.
- L1: Manual cleanup during occasional reviews.
- L2: Joiner-mover-leaver processes cover agents. Changes to model, prompt, or tool set trigger re-review.
- L3: Agents have defined expiry dates by default. Dormant agents are disabled automatically.
- L4: Lifecycle is fully policy-driven, with change detection on agent configuration.

**7. Monitoring and detection**
- L0: No agent-specific telemetry.
- L1: Agent activity is in logs somewhere, but nobody looks at it.
- L2: Agent activity flows to the SIEM or XDR with an agent identity attribute.
- L3: Behavioral baselines per agent. Detections for scope drift, unusual tool chaining, data volume anomalies, and prompt injection indicators.
- L4: Automated response: revoke tokens, suspend agent, or require human approval when risk crosses a threshold.

**8. Audit and forensics**
- L0: Cannot reconstruct what an agent did.
- L1: Partial logs, retained inconsistently.
- L2: Complete action log per agent, retained to the same standard as privileged human activity.
- L3: You can answer "who asked, which agent acted, which tools it used, what data it touched, and what it produced" for any action in the last 12 months.
- L4: Tamper-evident, end-to-end lineage suitable for regulators and litigation.

---

## Quick self-assessment

Answer yes or no. If you answer "no" to any question in a level, that is your current level.

**To claim Level 1**
- Can you produce a list of AI agents and AI-connected OAuth apps in your environment today?

**To claim Level 2**
- Does every agent on that list have a named owner who is still employed in the same role?
- Are all production agents off human personal credentials?
- Are agent secrets vaulted and rotated?
- Can you show which human each agent action was taken on behalf of?

**To claim Level 3**
- Are high-risk agents issued short-lived, task-scoped credentials?
- Do sensitive actions require human approval?
- Do you have detections tuned to agent behavior, not just generic service accounts?

**To claim Level 4**
- Is each tool call authorized in real time by a policy engine?
- Can you revoke a running agent's access in seconds with no human in the loop?

---

## The 90-day path from Level 1 to Level 2

This is where most organizations get the biggest risk reduction for the lowest cost.

| Days | Focus | Done looks like |
|---|---|---|
| 1 to 30 | **Find them** | Discovery across SaaS OAuth grants, cloud IAM, MCP connections, and developer platforms. Every agent tagged with a risk tier. |
| 31 to 60 | **Own them** | Named owner for every agent. Orphans suspended. Agents using human credentials migrated to dedicated identities. |
| 61 to 90 | **Constrain them** | Secrets vaulted and rotated. Top 20 over-scoped grants reduced. On-behalf-of attribution in logs. Agent activity flowing to the SIEM. |

**Metrics to report to the board**
- Percentage of agents with a named, current owner
- Percentage of agents using dedicated (non-human) credentials
- Median credential lifetime for high-risk agents
- Number of over-scoped grants remediated
- Mean time to revoke a compromised agent's access

---

## Anti-patterns to avoid

- **Treating agents as service accounts.** Vaulting the secret is necessary but not sufficient. Agents need purpose, owner, and behavior governance.
- **Treating agents as users.** Giving agents SSO seats and MFA prompts adds cost without adding control.
- **Blocking everything.** Teams will route around a "no" with personal accounts and browser extensions. Make the governed path the easy path.
- **Letting the agent inherit the human.** On-behalf-of should mean a constrained subset of rights, not a full copy.
- **Measuring the inventory, not the controls.** A list of 400 agents is not progress if 380 still run on long-lived keys.

---

## What this means for product teams

If you are building security or identity products, each level maps to a buyer problem:

- **L0 to L1:** Discovery. Buyers want visibility without deploying agents of their own. Integrations with SaaS admin APIs and cloud IAM win here.
- **L1 to L2:** Ownership and credential hygiene. Buyers want workflows that plug into existing IGA and PAM, not a new console.
- **L2 to L3:** Runtime authorization and delegation. This is the open product gap today. Whoever makes task-scoped, just-in-time access easy for developers will set the standard.
- **L3 to L4:** Continuous, policy-driven authorization across agents and organizations. Standards work (token exchange, workload identity, agent-to-agent protocols) will shape this layer.

The strongest products will meet buyers at Level 1 and give them a clear path to Level 3, without forcing them to rip out the identity stack they already own.

---

## Use this framework

This model is free to use and adapt. If you score your organization against it, I would like to hear where you landed and which capability was hardest to move.

*Author: Santosh, security product leader focused on identity governance for the enterprise and for AI agents. Views are my own.*

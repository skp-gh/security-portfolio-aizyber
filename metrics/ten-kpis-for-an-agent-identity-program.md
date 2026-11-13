---
title: "Ten KPIs for an Agent Identity Program That a CFO and Board Will Understand"
series: Agentic Identity Notes #5
type: Metrics
date: 2026-11-24
tags: [agentic-ai, non-human-identity, metrics, kpis, board-reporting, ciso, governance]
---

# Ten KPIs for an Agent Identity Program That a CFO and Board Will Understand

*Ten metrics, each with a formula, a data source, a suggested target, and the sentence that explains why a CFO should care. Use them to replace "we're working on it" with numbers that move.*

**Series:** Agentic Identity Notes #5 · **Type:** Metrics · **Read time:** 10 minutes

---

## TL;DR

- Boards are being asked to oversee cyber risk more directly, and AI agents are now part of that risk. Most security reporting still answers the wrong question: it counts activity (vulnerabilities, alerts, tickets) instead of exposure and control.
- The ten KPIs below measure whether agents are **known, owned, constrained, and stoppable**. They build on the metrics introduced in weeks 1 to 4 of this series.
- Lead with one number: **time to revoke everywhere**. It is the closest thing to "how bad could this get, and how fast can we stop it" that fits on one line.
- Every KPI comes with a starting target, not a standard. The value is in tracking the trend quarter over quarter and tying each number to a decision the board can make.

---

## The problem

**Who has it.** CISOs and IAM leaders who have to explain AI agent risk to a CFO, an audit committee, or a full board.

**Why now.** Two shifts make board-level reporting on this harder to avoid. In July 2023 the SEC adopted rules requiring public companies to disclose material cybersecurity incidents on Form 8-K (generally within four business days of determining materiality) and to describe their cybersecurity risk management, strategy, and governance, including board oversight, in annual reports. And NIST's Cybersecurity Framework 2.0, released in February 2024, added a Govern function that puts cybersecurity risk strategy, roles, and oversight alongside the technical functions. Neither is specific to AI agents. Both raise the expectation that leadership can describe cyber risk in terms of exposure and control, and agents are a fast-growing part of that exposure.

**What goes wrong today.** The typical security slide shows a vulnerability count, phishing click rates, and the number of tools deployed. None of those answer the questions a CFO actually asks about AI agents:

- How many do we have, and who is accountable for each one?
- What could one of them do if it were compromised?
- How fast can we shut one down, and how do we know it worked?

**What it costs to ignore.** Boards fill the gap with anxiety or with false comfort. Budget gets approved for the loudest problem instead of the largest one. And when an incident happens, there is no baseline to show what improved.

---

## The core idea: four questions, ten numbers

The KPIs are grouped by the four questions a board can hold onto. Each maps to the capabilities in the [Agentic Identity Maturity Model](../frameworks/agentic-identity-maturity-model.md).

| Question for the board | KPIs |
|---|---|
| **Do we know what we have?** | 1. Agent inventory coverage · 2. Owner coverage · 3. Orphaned agents |
| **Is each agent constrained?** | 4. Dedicated credential rate · 5. Standing secret exposure · 6. Over-scoped agents · 7. Third-party held credentials |
| **Can we stop one fast?** | 8. Time to revoke everywhere · 9. Verified revocation rate |
| **Can we prove what happened?** | 10. Attributable agent actions |

Targets below are **suggested starting points**. Set your own based on risk appetite, and report the trend.

---

### 1. Agent inventory coverage

- **Definition:** The share of discovered agents and AI-connected apps that are registered in your agent system of record with a risk tier.
- **Formula:** registered agents ÷ discovered agents (from OAuth grant logs, cloud IAM, MCP connections, endpoint discovery)
- **Why the CFO cares:** You cannot insure, budget for, or disclose risk you cannot count. This is the denominator for every other number.
- **Starting target:** 80% within 90 days, 95% steady state
- **Data source:** Discovery tooling and SaaS admin consoles, reconciled against the agent registry

### 2. Owner coverage

- **Definition:** The share of registered agents with a named, current business owner.
- **Formula:** agents with an owner who is active and in the same role ÷ registered agents
- **Why the CFO cares:** Accountability is a governance control. An agent with no owner is a cost with no budget holder and a risk with no one to answer for it.
- **Starting target:** 90%, rising to 100% for Tier 1 (high-risk) agents
- **Data source:** Agent registry joined to HR and IGA data

### 3. Orphaned agents

- **Definition:** Agents whose owner has left or changed roles with no transfer, and how long they stay that way.
- **Formula:** count of orphaned agents, plus median days from orphaning to reassignment or suspension
- **Why the CFO cares:** This is the classic access-review failure, now running automatically and around the clock.
- **Starting target:** Zero orphaned Tier 1 agents; median resolution under 7 days
- **Data source:** HR mover and leaver events matched to agent ownership

### 4. Dedicated credential rate

- **Definition:** The share of agents running on their own identity rather than a person's login or personal token.
- **Formula:** agents with dedicated identities ÷ registered agents
- **Why the CFO cares:** An agent on a human's credentials inherits everything that person can do, and its actions are indistinguishable from theirs in the logs.
- **Starting target:** 100% for production agents
- **Data source:** Agent registry, OAuth grant ownership, and IdP sign-in data

### 5. Standing secret exposure

- **Definition:** For high-risk agents, how much long-lived credential material exists.
- **Formula:** share of Tier 1 agents with any static secret, plus median credential age by tier
- **Why the CFO cares:** Long-lived secrets are what attackers steal and reuse. Shorter lifetimes shrink the window in which a stolen credential is worth anything.
- **Starting target:** 0% of Tier 1 agents with static secrets; median credential age inside your policy limit for every tier
- **Data source:** Vault, cloud IAM, and token issuance logs

### 6. Over-scoped agents

- **Definition:** Agents holding permissions they have not used.
- **Formula:** agents with one or more scopes unused in the last 30 days ÷ registered agents, plus the number of over-scoped grants remediated this quarter
- **Why the CFO cares:** Unused permissions are pure downside. They add blast radius without adding business value.
- **Starting target:** Under 20%, trending down each quarter
- **Data source:** OAuth grant scopes compared against API and audit logs

### 7. Third-party held credentials

- **Definition:** Agent credentials, such as refresh tokens, that live on a vendor's servers rather than yours, and whether each has a recorded holder and a revocation path.
- **Formula:** count of third-party held credentials, and the share with a documented holder and tested revocation method
- **Why the CFO cares:** This is your exposure to someone else's breach. When a vendor discloses an incident, this list is what you need in the first hour.
- **Starting target:** 100% have a recorded holder; 100% of Tier 1 have a tested revocation method
- **Data source:** OAuth grant inventory and vendor records

### 8. Time to revoke everywhere

- **Definition:** The elapsed time to revoke every credential and grant for one agent across every connected system, measured in a drill.
- **Formula:** median and slowest-system time across quarterly drills, by risk tier
- **Why the CFO cares:** This is the closest single number to "how much damage can happen before we stop it." It converts an abstract risk into a duration.
- **Starting target:** Tier 1 under 15 minutes, Tier 2 under 1 hour, Tier 3 under 24 hours
- **Data source:** Drill records (see the credential lifecycle PRD from week 4)

### 9. Verified revocation rate

- **Definition:** The share of revocations confirmed by a test rather than assumed.
- **Formula:** revocations verified by token introspection or a failed test call ÷ total revocations
- **Why the CFO cares:** A revocation that silently failed is worse than none, because everyone believes the risk is gone.
- **Starting target:** 100% for Tier 1; above 90% overall
- **Data source:** Revocation workflow logs

### 10. Attributable agent actions

- **Definition:** The share of agent actions logged with both the agent identity and, where relevant, the human it acted for.
- **Formula:** agent actions with agent ID and on-behalf-of principal recorded ÷ total agent actions sampled
- **Why the CFO cares:** After an incident, "we can't tell whether the person or their AI tool did it" is a legal, audit, and disclosure problem, not just a technical one.
- **Starting target:** 100% for Tier 1 systems; rising coverage elsewhere
- **Data source:** SIEM or XDR, sampled against SaaS audit logs

---

## How to apply it

### Step 1: Baseline in 30 days

Pick the five you can measure today. For most organizations that is inventory coverage, owner coverage, dedicated credential rate, over-scoped agents, and one time-to-revoke drill. Imperfect numbers are fine. The first quarter is about establishing a baseline, not a grade.

### Step 2: Tie each KPI to a decision

For every metric, write the decision it supports: fund discovery tooling, enforce "no registration, no credential," migrate Tier 1 agents off static secrets, or require vendors to document revocation. A metric without a decision attached becomes a vanity metric within two quarters.

### Step 3: Report on one page

A board slide should fit in a single view:

| Section | What goes there |
|---|---|
| **Headline** | Time to revoke everywhere for Tier 1, this quarter versus last |
| **Known and owned** | Inventory coverage, owner coverage, orphaned Tier 1 count |
| **Constrained** | Dedicated credential rate, Tier 1 standing secrets, over-scoped share, third-party held credentials |
| **Provable** | Verified revocation rate, attributable actions |
| **Asks** | One or two decisions needed from the board or CFO, with cost |

Use trend arrows and a target line. Leave the methodology in an appendix.

### Step 4: Review quarterly

Re-run the drill, refresh the numbers, and retire any KPI that has been at target for three quarters in favor of a harder one.

---

## Metrics for the metrics

You will know the scorecard is working when:

- The board asks follow-up questions about specific numbers rather than general reassurance.
- At least one budget decision per year is made on the basis of a KPI movement.
- Time to revoke everywhere and owner coverage both improve over two consecutive quarters.

---

## Tradeoffs and anti-patterns

- **Counting activity instead of exposure.** "Agents discovered this quarter" goes up whether you are getting safer or not. Report coverage and control, not effort.
- **Reporting the inventory as progress.** A list of 400 agents is not progress if most still run on long-lived keys. Pair every count with a control rate.
- **Too many numbers.** Ten is the maximum for a working scorecard. The board slide should lead with one and show no more than eight.
- **Targets presented as standards.** The starting targets here are suggestions. Present your own targets as risk appetite decisions the board owns.
- **Metrics nobody can reproduce.** If a number depends on one analyst's spreadsheet, it will not survive a change of staff or an audit. Document the formula and the data source.

**What I chose not to include:** cost per agent, AI tool spend, and model risk metrics such as hallucination rates. They matter, but they belong to AI governance and finance reporting. These ten stay focused on identity: who the agent is, what it can do, and how fast it can be stopped.

---

## What this means for product teams

- **Ship the scorecard, not just the console.** Buyers need these numbers on a page they can hand to a CFO. Products that calculate them out of the box will win budget conversations.
- **Time to revoke is a product feature.** If your product can run and record a revocation drill, you give the buyer their most persuasive board metric.
- **The holder field matters again.** KPI 7 is impossible without knowing which credentials live with third parties. Most products still do not model it.
- **Make metrics reproducible.** Expose the formula and the raw data behind every number, so the CISO can defend it in front of auditors.

---

## Sources

- U.S. Securities and Exchange Commission, "SEC Adopts Rules on Cybersecurity Risk Management, Strategy, Governance, and Incident Disclosure by Public Companies" (press release 2023-139, July 2023): https://www.sec.gov/news/press-release/2023-139
- NIST, The NIST Cybersecurity Framework (CSF) 2.0 (February 2024): https://nvlpubs.nist.gov/nistpubs/CSWP/NIST.CSWP.29.pdf
- Protiviti, "NIST Releases Version 2.0 of Its Cybersecurity Framework (CSF)": https://www.protiviti.com/us-en/flash-report/nist-releases-version-20-its-cybersecurity-framework-csf
- Agentic Identity Notes #1, The Agentic Identity Maturity Model: [frameworks/agentic-identity-maturity-model.md](../frameworks/agentic-identity-maturity-model.md)
- Agentic Identity Notes #4, PRD: Agent Credential Lifecycle Management: [prds/agent-credential-lifecycle-management.md](../prds/agent-credential-lifecycle-management.md)

---

*Author: Santosh, security product leader focused on identity governance for the enterprise and for AI agents. Views are my own. Free to use and adapt with attribution.*

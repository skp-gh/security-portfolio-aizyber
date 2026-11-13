---
title: "PRD: Agent Credential Lifecycle Management (Issue, Scope, Rotate, Revoke)"
series: Agentic Identity Notes #4
type: PRD
date: 2026-11-17
tags: [agentic-ai, non-human-identity, credentials, secrets, oauth, pam, prd]
---

# PRD: Agent Credential Lifecycle Management

*A product requirements doc for the credentials AI agents use, from the moment one is issued to the moment it is provably dead. Hand it to a platform team or a vendor and you should get the same answer back from both.*

**Series:** Agentic Identity Notes #4 · **Type:** PRD · **Read time:** 12 minutes

---

## TL;DR

- Most agent credential risk lives in the gap between the agent and its credentials. The agent has an identity record somewhere. Its keys, tokens, and OAuth grants live in five other places, and nobody can list them in one view.
- This PRD treats credentials as a lifecycle bound to the agent: every credential is **issued** only to a registered agent, **scoped** to a declared purpose, **rotated** on a schedule and on events, and **revocable** through one operation.
- The requirement that matters most is **revoke everywhere**: one action that kills every credential and grant for one agent across every connected system, with a measured time and verified results. Build and drill that first, even though it happens last.
- Prefer short-lived, audience-bound tokens issued at runtime over stored secrets. Where that is not possible (legacy apps, third-party SaaS), make the stored secret visible, owned, and rotatable, and record **who holds it**.

---

## The problem

**Who has it.** CISOs, IAM and PAM teams, platform engineers building agents, and SOC responders who get the call when a vendor has an incident.

**Why now.** Agents are being connected to business systems faster than credential practices are adapting. The shape of the risk is already public. In August 2025, attackers used stolen OAuth and refresh tokens tied to the Drift AI chat agent to pull data from customer Salesforce instances; Google's threat intelligence team reported the activity ran from as early as August 8 through at least August 18, 2025. Salesloft revoked the Drift connections to Salesforce, Salesforce invalidated active access and refresh tokens and removed Drift from AppExchange, and customers were told to revoke and rotate credentials. The containment step was revocation. The hard part for many organizations was knowing which credentials existed in the first place.

The standards and guidance bodies are pointing at the same gap. OWASP's Top 10 for Agentic Applications (December 2025) includes Identity and Privilege Abuse as one of its ten risks. NIST's NCCoE published a concept paper in February 2026 on software and AI agent identity and authorization, asking industry for input on how agents are identified, authorized, and audited, and naming OAuth, OpenID Connect, SPIFFE/SPIRE, and SCIM among the relevant standards.

**What it costs to ignore.**

- Incident response measured in days, because nobody can enumerate what to revoke.
- Rotation policies that exist on paper. The first rotation that breaks a production integration is usually the last one anyone schedules.
- Credentials that outlive the agent, the owner, and sometimes the vendor relationship.
- A maturity score stuck at Level 1 of the [Agentic Identity Maturity Model](../frameworks/agentic-identity-maturity-model.md), where agents are inventoried but still run on long-lived secrets.

---

## Goals and non-goals

**Goals**

1. Every credential an agent can use is linked to a registered agent with a current owner, purpose, and risk tier.
2. High-risk agents run on short-lived, runtime-issued credentials with no standing secrets.
3. Rotation happens on schedule and on events without breaking production.
4. Any agent can be fully cut off, everywhere, within a target time set by its risk tier, and the result is verified.

**Non-goals**

- Replacing the existing vault, PAM, IdP, or IGA platform. This capability orchestrates them.
- Managing human credentials.
- Model-level defenses such as prompt injection filtering. Those matter, and they are adjacent: a well-scoped, short-lived credential limits what a manipulated agent can do.

---

## Personas

| Persona | What they need |
|---|---|
| **Agent owner** (business) | Know what their agent can access, approve scope changes, and not be surprised by an outage. |
| **Platform engineer** | Get a credential for a new agent in minutes, without filing a ticket or reaching for a personal token. |
| **IAM / PAM admin** | One view of every agent credential, its holder, and its age. Policy that enforces itself. |
| **SOC responder** | A single action to cut an agent off, and proof that it worked. |
| **Auditor** | Evidence of who approved each credential, when it rotated, and when it died. |

---

## The core idea: credentials as a lifecycle bound to the agent

### The object model

Most tools track one of these entities well. Few track the links between them.

| Entity | Key fields |
|---|---|
| **Agent** | ID, owner, purpose, risk tier, status (from the agent system of record) |
| **Credential** | Type, issuer, audience, scopes, created, expires, last used, version |
| **Grant** | A delegated authorization at a third party (for example an OAuth consent), the human who granted it, scopes |
| **Holder** | Where the credential physically lives: our vault, the agent runtime, a CI system, or **a third-party vendor's servers** |

The **holder** field is the one most programs miss. A refresh token held by a SaaS vendor cannot be rotated by you; it can only be revoked at the issuer. If you do not record the holder, you discover this during an incident.

### Credential classes

| Class | Examples | Policy | Lifecycle approach |
|---|---|---|---|
| **Runtime-issued, short-lived** | Workload identity federation, SPIFFE SVIDs, OAuth 2.0 Token Exchange (RFC 8693) | Preferred | Expiry is the rotation. Revocation means stop issuing and revoke any refresh capability. |
| **Delegated grant held by a third party** | A user-consented OAuth grant to an AI app, with the refresh token stored by the vendor | Tolerated with controls | Track the grant and holder, review scopes, revoke at the issuer (RFC 7009 where supported). |
| **Stored static secret** | API keys, service account passwords, personal access tokens issued to a service | Legacy, migrate | Vault it, rotate on schedule and on events, hunt for copies. |
| **Human credential used by an agent** | An employee's session cookie or personal token | Prohibited | Detect, suspend, migrate to a dedicated identity. |

### Policy by risk tier (starting points, not standards)

| Risk tier | Typical agent | Max access token lifetime | Static secrets | Max static secret age | Time-to-revoke target |
|---|---|---|---|---|---|
| **Tier 1** | Moves money, changes production, touches regulated data | 15 minutes | Not allowed | n/a | 15 minutes |
| **Tier 2** | Writes to business systems | 1 hour | By documented exception | 30 days | 1 hour |
| **Tier 3** | Read-only, internal data | 8 hours | Allowed | 90 days | 24 hours |

Tune these to your environment. The point is that the numbers are written down per tier, enforced by policy, and measured.

---

## Requirements

Priority: **P0** is required for launch, **P1** within two quarters, **P2** is a target state.

### Issue

| ID | Requirement | Priority |
|---|---|---|
| ISS-1 | Credentials can only be issued to an agent registered in the system of record with a current owner, purpose, and risk tier. No registration, no credential. | P0 |
| ISS-2 | Every credential record stores agent ID, type, issuer, holder, audience, scopes, created, expiry, and last used. | P0 |
| ISS-3 | No agent may be issued a human's personal credential. Agent traffic on a human token is detected and flagged to the owner and SOC. | P0 |
| ISS-4 | Default to short-lived tokens obtained at runtime. A static secret requires a documented exception with its own expiry date. | P1 |
| ISS-5 | Tokens for MCP servers and APIs are audience-bound to the specific resource (RFC 8707 resource indicators, which the MCP authorization specification requires clients to use). Servers reject tokens not issued for them. | P1 |
| ISS-6 | Tier 1 agents use sender-constrained tokens (DPoP, RFC 9449, or mutual TLS, RFC 8705) so a stolen token is far less useful on its own. | P2 |
| ISS-7 | Self-service issuance for registered agents completes in minutes. If the governed path is slower than a personal token, teams will use the personal token. | P0 |

### Scope

| ID | Requirement | Priority |
|---|---|---|
| SCP-1 | Scopes are declared at registration as part of the agent's purpose. A credential request beyond declared scope requires owner and security approval. | P0 |
| SCP-2 | Delegated (on-behalf-of) credentials represent both the user and the agent, for example through token exchange with an actor claim, and never exceed the user's own rights. | P0 |
| SCP-3 | Scopes not exercised in 30 days are flagged for removal, with the owner notified. | P1 |
| SCP-4 | High-risk actions require step-up approval or a separate, task-scoped credential that expires when the task ends. | P1 |
| SCP-5 | Any scope increase triggers owner re-attestation and is logged as a change event. | P2 |

### Rotate

| ID | Requirement | Priority |
|---|---|---|
| ROT-1 | Maximum lifetimes are enforced per risk tier. Credentials past their limit are rotated automatically or suspended. | P0 |
| ROT-2 | Rotation is zero-downtime: the new version is issued and validated before the old one is retired, with a short overlap. A failed rotation alerts the owner. It never silently keeps the old secret. | P0 |
| ROT-3 | Event-driven rotation: rotate on owner change, agent configuration change (model, prompt, or tool set), suspected exposure, or a security incident at a vendor that holds the credential. | P1 |
| ROT-4 | Copy detection: secret scanning across code repos, CI variables, tickets, and wikis. A credential found outside its recorded holder is rotated and the copy logged. | P1 |
| ROT-5 | Credentials that cannot be rotated programmatically are reported as a named risk with an owner and a migration date. | P2 |

### Revoke

| ID | Requirement | Priority |
|---|---|---|
| REV-1 | **Revoke everywhere**: one action for an agent revokes every credential and grant across connected systems, disables the agent identity, and records the result per system. | P0 |
| REV-2 | Time to revoke is measured against the tier target in scheduled drills, reported as both median and slowest system. | P0 |
| REV-3 | Revocation is verified, not assumed. After revoke, each credential is tested (token introspection or a test call) and any system where verification fails is escalated. | P0 |
| REV-4 | Revocation covers refresh tokens, sessions, and the underlying grant, not only access tokens. | P0 |
| REV-5 | **Holder mode**: revoke every credential held by one third party, across all agents, in a single operation. This is the action you need when a vendor discloses an incident. | P1 |
| REV-6 | Automatic revocation triggers: owner leaves with no transfer within a set window, agent dormant past its tier limit, agent decommissioned, or a static secret exception lapses. | P1 |
| REV-7 | Emit and consume OpenID Shared Signals (SSF, CAEP) events where systems support them, so revocation propagates in near real time. Fall back to direct API calls (RFC 7009) and documented manual runbooks for everything else. | P1 |

### Non-functional requirements

- **Integration first.** API and event driven. Works with the existing vault, PAM, IdP, IGA, and SIEM. No new console required for daily work.
- **Failure mode.** Issuance for Tier 1 fails closed. Tier 3 may use a short cached token during an issuer outage. This is a deliberate availability tradeoff, documented per tier.
- **Audit.** Every issue, scope change, rotation, and revocation is logged with the agent, the human approver or trigger, and the result, and retained to the same standard as privileged human activity.
- **Coverage reporting.** The system reports which connected systems support automated revocation and which need a manual step, so nobody assumes coverage they do not have.

---

## How to apply it

### Phase 1, days 1 to 30: see it and drill it

- Join the credential inventory (vault, cloud IAM, OAuth grant logs from your SaaS admin consoles, CI secrets) to the agent registry. Every credential gets an agent, an owner, and a holder, or goes on an orphan list.
- Write a revoke-everywhere runbook for your ten most connected agents.
- Run one drill on one agent. Time it per system. Write down where you got stuck.

### Phase 2, days 31 to 60: make policy enforce itself

- Turn on "no registration, no credential" at the vault and the IdP.
- Set lifetimes by tier and start reporting credential age.
- Wire owner changes from HR and IGA events to rotation and re-attestation.

### Phase 3, days 61 to 90: shrink what can be stolen

- Migrate Tier 1 agents to runtime-issued, audience-bound tokens.
- Automate revocation verification for the systems that support it.
- Add holder mode, and connect Shared Signals where your IdP and key SaaS apps support it.

### Build or buy: questions for any platform or vendor

1. Can you show me every credential for one agent, including grants held by third parties?
2. Do you record the holder of each credential?
3. Show me revoke everywhere on a live agent. How long did it take, and how did you verify it?
4. How do you rotate without downtime, and what happens when a rotation fails?
5. Which of our systems can you revoke automatically, and which need a human?

---

## Metrics

- **Credential linkage:** percentage of agent credentials linked to a registered agent with a current owner and holder.
- **Standing secrets:** percentage of Tier 1 agents with zero static secrets.
- **Credential age:** median and 95th percentile age by tier, against the policy limit.
- **Time to revoke everywhere:** from drills, median and slowest system, by tier.
- **Verified revocation rate:** percentage of revocations confirmed by a test, not assumed.
- **Rotation failure rate:** failed or rolled-back rotations per month. If this is high, teams will start asking for exceptions.
- **Copies found:** credentials found outside their recorded holder per month. It should spike when you start scanning, then trend down.

---

## Tradeoffs and anti-patterns

- **Treating the vault as the lifecycle.** A vault stores and rotates a secret. It usually does not know the agent's owner, purpose, or the third party that holds a copy.
- **Extending lifetimes because rotation breaks things.** Fix rotation. A long-lived secret is the cost of a brittle integration, paid later with interest.
- **Revoking the access token and calling it done.** If the refresh token or the grant survives, the agent (or whoever stole its credentials) comes back in an hour.
- **Assuming revocation worked.** Revocation APIs fail, time out, and get skipped for systems nobody remembered. Verification is the requirement.
- **Making the governed path slow.** If issuance takes a ticket and a week, engineers will reach for a personal token, and you are back to Level 0.

**What I chose not to require:** per-action credentials for every agent. The latency and complexity are real, and most agents do not need it. It is reserved for Tier 1 high-risk actions in SCP-4. I also left out key management for agents that sign their own outputs. That deserves its own treatment.

**Open questions**

- How do we get timely revocation from third-party vendors that hold refresh tokens and offer no revocation API?
- How much downstream SaaS key rotation can be automated before connector maintenance becomes its own program?
- Who approves scope exceptions when there are thousands of agents, and how do we stop that approval from becoming a rubber stamp?

---

## What this means for product teams

- **Revoke everywhere is a product, not a script.** The connectors to each SaaS and cloud revocation API, with verification, are hard to build and hard to copy. That is where defensibility sits.
- **The holder field is missing from most products.** Whoever models "this credential lives on a vendor's servers, and here is how to kill it" will be the tool responders open first during a supply chain incident.
- **Verification is underbuilt.** Buyers will pay for proof that revocation worked, because that is what they have to report to the board and to regulators.
- **Rotation that never breaks production wins adoption.** Overlapping versions, health checks, and automatic rollback matter more to platform teams than any dashboard.
- **Standards lower the cost.** Token exchange, resource indicators, sender-constrained tokens, and Shared Signals already exist. The product opportunity is making them the default path for developers rather than an expert configuration.

---

## Sources

- The Hacker News, report on the Salesloft Drift OAuth token theft (August 27, 2025, updated), citing Google Threat Intelligence Group, Salesloft, and Salesforce statements: https://thehackernews.com/2025/08/salesloft-oauth-breach-via-drift-ai.html
- OWASP GenAI Security Project, Top 10 for Agentic Applications (December 9, 2025): https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/
- NIST NCCoE, concept paper "Accelerating the Adoption of Software and AI Agent Identity and Authorization" (February 5, 2026): https://csrc.nist.gov/pubs/other/2026/02/05/accelerating-the-adoption-of-software-and-ai-agent/ipd
- OpenID Foundation, Shared Signals Framework 1.0, CAEP 1.0, and RISC 1.0 approved as Final Specifications (September 16, 2025): https://openid.net/press-release-openid-foundation-finalizes-global-standards-for-real-time-identity-security/
- Model Context Protocol, Authorization specification (2025-11-25): https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization
- IETF RFC 7009, OAuth 2.0 Token Revocation: https://datatracker.ietf.org/doc/rfc7009/
- IETF RFC 8693, OAuth 2.0 Token Exchange: https://datatracker.ietf.org/doc/rfc8693/
- IETF RFC 8707, Resource Indicators for OAuth 2.0: https://datatracker.ietf.org/doc/rfc8707/
- IETF RFC 8705, OAuth 2.0 Mutual-TLS Client Authentication and Certificate-Bound Access Tokens: https://datatracker.ietf.org/doc/rfc8705/
- IETF RFC 9449, OAuth 2.0 Demonstrating Proof of Possession (DPoP): https://datatracker.ietf.org/doc/rfc9449/
- IETF RFC 9700, Best Current Practice for OAuth 2.0 Security (January 2025): https://datatracker.ietf.org/doc/rfc9700/
- SPIFFE project: https://spiffe.io/

---

*Author: Santosh, security product leader focused on identity governance for the enterprise and for AI agents. Views are my own. Free to use and adapt with attribution.*

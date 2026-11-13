---
title: "Threat Model: An AI Agent with Delegated OAuth Access to SaaS Apps"
series: Agentic Identity Notes #2
type: Threat model
date: 2026-11-03
tags: [agentic-ai, oauth, saas-security, threat-modeling, stride, non-human-identity]
---

# Threat Model: An AI Agent with Delegated OAuth Access to SaaS Apps

*A STRIDE walkthrough of the most common agent pattern in the enterprise today, with the trust boundaries, the threats that matter most, and the controls that actually reduce them.*

**Series:** Agentic Identity Notes #2 · **Type:** Threat model · **Read time:** 10 minutes

---

## TL;DR

- The most common AI agent in your environment is not a custom build. It is a third-party AI app that a user connected to a SaaS tool by clicking "Allow" on an OAuth consent screen.
- That click creates a **refresh token** held by a vendor you may never have assessed. The token usually outlives the user's attention, the project, and sometimes the user's employment.
- Walking the pattern through STRIDE, the highest risks are **Elevation of Privilege** (broad scopes plus prompt injection) and **Information Disclosure** (bulk export with a stolen token). Spoofing and Repudiation are close behind because most logs cannot tell the agent from the human.
- The controls that move the needle are boring: admin-gated consent, scope allowlists, short token lifetimes with rotation, IP or sender constraints, on-behalf-of attribution in logs, and a tested "revoke everything for this app" runbook.

---

## The problem

In week one I described a sales ops analyst who connected an AI tool to the CRM with her own login. This piece takes that exact pattern apart.

It matters now for two reasons. First, the pattern is everywhere: any user with consent rights can create one in under a minute, and many SaaS platforms allow user consent for at least some scopes unless an admin turns it off. Second, it is no longer hypothetical. In August 2025, Google Threat Intelligence reported that an actor tracked as UNC6395 used compromised OAuth tokens from the Salesloft Drift integration, an AI chat product, to run queries against and export data from many customers' Salesforce instances, then searched that data for further secrets like AWS keys and Snowflake tokens. GTIG later confirmed that tokens for a separate Drift email integration were used to access a small number of Google Workspace mailboxes. The core platforms were not breached. The delegated tokens were.

The cost of ignoring this: a vendor you approved once (or never approved at all) becomes a standing, unmonitored path into your most sensitive SaaS data.

---

## The system being modeled

**Scenario:** A user connects a third-party AI assistant ("the agent") to two SaaS apps, a CRM and a corporate email and file suite. The agent reads records, drafts emails, updates fields, and runs scheduled jobs while the user is offline.

### Components

| # | Component | Owner | Notes |
|---|---|---|---|
| C1 | The user (resource owner) | Your org | Grants consent. Changes roles. Eventually leaves. |
| C2 | Corporate IdP | Your org | Authenticates the user. Often not in the loop after consent. |
| C3 | SaaS authorization server | SaaS vendor | Issues access and refresh tokens to the agent. |
| C4 | SaaS resource APIs (CRM, mail, files) | SaaS vendor | Enforce scopes. Log API calls, often attributed to the user. |
| C5 | Agent vendor backend | Agent vendor | Stores refresh tokens. Runs the model and the job scheduler. |
| C6 | LLM and tool layer | Agent vendor (or its model provider) | Turns content and instructions into API calls. |
| C7 | Untrusted content | Anyone | Emails, CRM notes, shared documents, web pages the agent reads. |

### Data flow

1. User clicks "Connect" in the agent. Agent redirects to the SaaS authorization server.
2. User authenticates (via the corporate IdP) and approves the requested scopes.
3. SaaS issues an authorization code. Agent backend exchanges it for an access token and a refresh token.
4. Agent backend stores the refresh token. From here on, the user does not need to be present.
5. On each task, the agent refreshes an access token, reads data (including C7 content), the model decides on actions, and the agent calls SaaS APIs.
6. SaaS logs the calls. In many platforms the actor field shows the user, with the app ID in a secondary field, if at all.

### Trust boundaries

| Boundary | Between | Why it matters |
|---|---|---|
| **TB1** | User and consent screen | The user is making a security decision with little context. Scopes are written for developers. |
| **TB2** | Your tenant and the agent vendor backend | Long-lived refresh tokens leave your control here. This is the boundary UNC6395 exploited. |
| **TB3** | Agent vendor and the model or tool layer | The model's output becomes an API call. Instructions and data share one channel. |
| **TB4** | Untrusted content and the model's context | Anything the agent reads can try to steer what it does (indirect prompt injection). |
| **TB5** | SaaS API and your security monitoring | If logs do not separate agent from human, detection and forensics fail here. |

---

## STRIDE walkthrough

Likelihood (L) and Impact (I) are scored 1 to 3 for a typical mid-to-large enterprise with default SaaS consent settings. Adjust for your own environment.

| STRIDE | Threat | Where | L | I | Key controls |
|---|---|---|---|---|---|
| **Spoofing** | Consent phishing: an attacker registers a look-alike "AI assistant" and tricks users into granting it access. | TB1 | 3 | 2 | Restrict user consent to verified publishers and low-risk scopes. Admin consent workflow for everything else. |
| **Spoofing** | Stolen refresh token replayed from attacker infrastructure, indistinguishable from the real agent. | TB2 | 2 | 3 | Sender-constrained tokens (DPoP or mTLS) where supported. IP restrictions on the connected app. Short refresh token lifetimes with rotation. |
| **Tampering** | Indirect prompt injection in an email or CRM note causes the agent to modify records, forward mail, or change sharing settings. | TB4, TB3 | 3 | 2 | Read-only scopes by default. Human approval for write actions on sensitive objects. Separate agents for read and write. |
| **Tampering** | Agent vendor ships a model, prompt, or tool update that changes behavior with the same token. | TB3 | 2 | 2 | Contractual change notification. Behavioral baseline per app. Re-review on material vendor change. |
| **Repudiation** | Actions taken by the agent are logged as the user. Neither party can prove who did what. | TB5 | 3 | 2 | Require app ID or client ID in audit logs. Ingest SaaS logs into the SIEM with an "acting via app" attribute. |
| **Information disclosure** | Bulk export with a stolen token: count queries, then large pulls of accounts, contacts, cases, mailboxes. | TB2, C4 | 2 | 3 | Least-privilege scopes (no "full" or "all mail"). Volume and query-pattern detections per app. API rate and export limits. |
| **Information disclosure** | Agent surfaces secrets stored in SaaS records (keys, passwords pasted into tickets) to the model or to an attacker. | C4, C6 | 3 | 3 | Secret scanning on SaaS data. Treat SaaS objects as possible secret stores. Rotate anything found. |
| **Information disclosure** | Agent vendor retains or trains on customer data beyond the stated purpose. | C5, C6 | 2 | 2 | Vendor assessment that covers token storage and data retention. Contract terms. |
| **Denial of service** | A runaway agent loop exhausts API quotas, locking out the real business process that depends on the same tenant limits. | C4 | 2 | 1 | Per-app rate limits. Alert on quota consumption by app. |
| **Denial of service** | Emergency revocation of a widely used app breaks workflows nobody documented. | Response | 2 | 2 | Know which teams depend on each app before the incident. Practice revocation. |
| **Elevation of privilege** | Agent holds the user's full delegated rights, so any compromise (injection, vendor breach, token theft) inherits everything the user can do. | TB1, C4 | 3 | 3 | Scope allowlists per app. Task-scoped tokens or token exchange for narrower audiences. Never grant admin-consented tenant-wide scopes to a user-convenience tool. |
| **Elevation of privilege** | User changes role or leaves; the refresh token keeps working with stale or accumulated rights. | C1, C3 | 3 | 2 | Revoke app grants on mover and leaver events. Periodic grant review tied to owner attestation. |

### The top five, ranked

1. **Over-broad delegated scopes** (EoP). Everything else gets worse because of this one. If the token can only read three objects, a stolen token can only read three objects.
2. **Refresh tokens held outside your boundary with no sender constraint** (Spoofing, Information disclosure). This is the TB2 problem, and the Drift campaign shows how it plays out at scale.
3. **Agent actions logged as the human** (Repudiation). You cannot detect what you cannot attribute.
4. **Indirect prompt injection driving write actions** (Tampering). The newest threat on this list, and the one where controls are least mature. OWASP's Top 10 for Agentic Applications (December 2025) lists Agent Goal Hijack and Identity and Privilege Abuse as its first and third risks.
5. **Grants that survive role changes** (EoP). Old-fashioned joiner-mover-leaver failure, now with a token attached.

---

## How to apply it

### Run this model on your own top five AI apps (half a day)

1. **Pull the grant inventory.** From each major SaaS admin console, export third-party OAuth apps, the users who granted them, scopes, and last-used date. Filter for AI and automation tools.
2. **Pick the five with the broadest scopes or the most users.** That is your sample.
3. **For each, answer five questions:**
   - Who consented: the user or an admin?
   - What can the token do that the stated use case does not need?
   - Where does the refresh token live, and how long does it last?
   - Would our logs show this app, or only the user?
   - If we revoked it right now, what breaks and who would call us?
4. **Score each row of the STRIDE table** for each app. Anything scoring 3 on both L and I goes on the remediation list.
5. **Write the revocation runbook** for the top app before you need it. Which admin consoles, which API calls, who to notify, how to rotate secrets the app could see.

### Controls by maturity level

This maps to the [Agentic Identity Maturity Model](../frameworks/agentic-identity-maturity-model.md).

| Level | Controls for delegated OAuth agents |
|---|---|
| **L1** | Quarterly export of OAuth grants from top SaaS apps. AI apps flagged. |
| **L2** | User consent restricted. Admin consent workflow live. Every approved app has an owner and an approved scope list. Grants revoked on leaver events. App ID present in SIEM data. |
| **L3** | Scope allowlists enforced. Refresh token lifetime and IP restrictions set per app. Write actions on sensitive objects need human approval. Detections for bulk export and unusual query patterns per app. |
| **L4** | Tokens are sender-constrained or exchanged per task for narrow audiences. Grant risk is continuously evaluated, and suspicious apps are suspended automatically through shared signals between the SaaS platform and your security stack. |

---

## Metrics

- **Percentage of AI app grants that went through admin consent** (target: 100% for anything beyond basic profile scopes)
- **Number of AI apps holding high-risk scopes** (full mailbox, full CRM, file-wide read or write), trending down
- **Median refresh token age** for AI apps
- **Percentage of SaaS audit events where the acting app is identifiable**
- **Time to revoke all tokens for one app across all tenants**, measured in a drill

---

## Tradeoffs and anti-patterns

- **Blocking all user consent overnight.** It works for a week. Then people use personal accounts and browser extensions you cannot see at all. Pair restriction with a fast, friendly approval path.
- **Treating the vendor questionnaire as the control.** A SOC 2 report does not tell you how long the vendor keeps your refresh tokens or whether they are bound to anything.
- **Approving scopes once, forever.** Agents add features. Re-review when the vendor asks for new scopes, and treat a scope increase like a privilege escalation request.
- **Assuming the IdP sees it.** After step 2 of the data flow, the corporate IdP is usually out of the picture. Conditional access policies at login do not govern a refresh token used three months later from a vendor's cloud.
- **Ignoring secrets in SaaS data.** The Drift actor's main goal, per GTIG, was harvesting credentials from the exported records. Your CRM tickets are a secret store whether you meant them to be or not.

What I chose not to cover here: agents you build yourself on your own cloud (a workload identity problem), and MCP server connections. MCP gets its own threat model in week 7.

---

## What this means for product teams

- **SaaS platforms** are in the strongest position. Per-app sender constraints, default-short refresh tokens for third-party apps, and first-class "acting via app" fields in audit logs would close several rows of this table for every customer at once.
- **Identity and SSPM vendors** have a clear buyer problem: one view of every delegated grant across SaaS tenants, with scope risk scoring and one-click revocation. The integration surface (admin APIs per platform) is the moat.
- **Agent vendors** that publish where tokens live, how long they last, and how customers can constrain them will win security reviews faster. Today most do not.
- **The open gap** is runtime: deciding per action whether an agent acting for a specific person should be allowed to do a specific thing right now. Token exchange (RFC 8693) and sender-constrained tokens are the building blocks, but almost no one has made them easy.

---

## Sources

- Google Threat Intelligence Group, "Widespread Data Theft Targets Salesforce Instances via Salesloft Drift" (August 2025, updated August 28, 2025): https://cloud.google.com/blog/topics/threat-intelligence/data-theft-salesforce-instances-via-salesloft-drift
- IETF RFC 9700, Best Current Practice for OAuth 2.0 Security (January 2025): https://datatracker.ietf.org/doc/rfc9700/
- IETF RFC 8693, OAuth 2.0 Token Exchange: https://datatracker.ietf.org/doc/rfc8693/
- OWASP GenAI Security Project, Top 10 for Agentic Applications (December 2025): https://genai.owasp.org/2025/12/09/owasp-genai-security-project-releases-top-10-risks-and-mitigations-for-agentic-ai-security/
- Microsoft Learn, Detect and remediate illicit consent grants: https://learn.microsoft.com/en-us/defender-office-365/detect-and-remediate-illicit-consent-grants
- Microsoft Learn, Threat modeling framework (STRIDE): https://learn.microsoft.com/en-us/training/modules/tm-use-a-framework-to-identify-threats-and-find-ways-to-reduce-or-eliminate-risk/1b-threat-modeling-framework

---

*Author: Santosh, security product leader focused on identity governance for the enterprise and for AI agents. Views are my own. Free to use and adapt with attribution.*

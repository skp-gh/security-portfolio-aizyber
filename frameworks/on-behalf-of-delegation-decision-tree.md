---
title: "On-Behalf-Of Done Right: A Delegation Decision Tree for Agent Access"
series: Agentic Identity Notes #8
type: Framework
date: 2026-12-15
tags: [agentic-ai, delegation, oauth, token-exchange, non-human-identity, iam, framework]
---

# On-Behalf-Of Done Right: A Delegation Decision Tree for Agent Access

*A five-question decision tree for choosing how an AI agent gets access: with its own identity, a delegated slice of a person's rights, an enterprise-managed grant, a user-consented grant, or a human approval in the loop. Use it in design reviews so every agent's access pattern is a decision, not an accident.*

**Series:** Agentic Identity Notes #8 · **Type:** Framework · **Read time:** 10 minutes

---

## TL;DR

- "On behalf of" should mean **a slice of a person's rights, never a copy**. Most agent access today is either impersonation (the agent uses the person's own token) or a shared service token with far more power than any single user.
- Five questions pick the right pattern for any agent action: Is a human asking? Is the resource inside your trust domain? Do both apps support enterprise-managed authorization? Is the action high risk? Is another agent involved?
- The patterns already have standards behind them: OAuth 2.0 Token Exchange (RFC 8693) with an actor claim, the Identity Assertion JWT Authorization Grant (an IETF draft behind Cross App Access), Rich Authorization Requests (RFC 9396), and OpenID CIBA for out-of-band approval.
- The test of a good delegation design is the log line: every action should show **the person, the agent, the chain between them, and the scope that was used**.

---

## The problem

Delegation is where agent identity gets subtle. In week one's [maturity model](agentic-identity-maturity-model.md), Delegation and consent is the capability where most organizations score lowest, because the default patterns blur two identities into one.

Two shortcuts show up again and again:

1. **Impersonation.** The agent runs on the person's own session or token. It can do everything they can do, and downstream logs show the person, not the agent.
2. **The shared service token.** One powerful service account serves every user. The agent decides, in its own code, what each user should be allowed to see. That is a confused deputy waiting for a prompt injection.

Both work in a demo. Both fail an audit question as simple as "who did this, and were they allowed to?" OWASP's Top 10 for Agentic Applications lists Identity and Privilege Abuse among its ten risks for exactly this reason.

The cost of ignoring it: agents that quietly hold more rights than the people they serve, logs that cannot separate human actions from agent actions, and no clean way to stop one agent without disrupting the person behind it.

---

## The core idea: five patterns, five questions

### The five access patterns

| Pattern | Use it when | What the token says | Who decides | Main risk to manage |
|---|---|---|---|---|
| **A. Agent's own identity** (autonomous) | No human is asking. Scheduled jobs, event-driven work, back-office automation. | The agent is the subject. No user involved. | Agent owner and policy | Scope creep over time |
| **B. Delegated via token exchange** | A human is asking and the resource trusts your authorization server. | The person is the subject; the agent is the actor (RFC 8693 `act` claim). Scopes are the intersection of the person's rights, the agent's purpose, and the task. | Your authorization server, by policy | Getting the intersection wrong |
| **C. Enterprise-managed authorization** | A human is asking, the resource is a third-party app, and both apps support it. | An identity assertion from your IdP is exchanged for an access token at the app (ID-JAG, also called Cross App Access). | Your IdP, by policy, instead of each user | Limited app support today |
| **D. User-consented OAuth grant** | A human is asking, the resource is third party, and pattern C is not available. | A standard OAuth grant the user approved, often with a refresh token held by the vendor. | The user, gated by admin approval | Over-scoping and long-lived refresh tokens |
| **E. Human-in-the-loop step-up** | Layered on B, C, or D when an action is high risk. | A short-lived, task-scoped credential that describes the exact action approved (for example with RFC 9396 `authorization_details`). | The person, at the moment of action | Approval fatigue |

### The decision tree

Ask these in order for each agent, and for each kind of action it takes.

```
Q1. Is a specific person asking for this action right now?
    NO  ->  Pattern A: agent's own identity, scoped to its declared purpose.
            Never borrow a person's token for unattended work.  Go to Q4.
    YES ->  Q2

Q2. Does the target resource trust your authorization server
    (internal APIs, MCP servers you run, resources behind your gateway)?
    YES ->  Pattern B: token exchange.  Subject = person, actor = agent,
            scope = person's rights AND agent purpose AND this task.  Go to Q4.
    NO  ->  Q3  (third-party SaaS)

Q3. Do both the agent's app and the target app support
    enterprise-managed authorization (ID-JAG / Cross App Access)?
    YES ->  Pattern C: enterprise-managed authorization.  Go to Q4.
    NO  ->  Pattern D: user-consented grant, behind admin approval,
            a scope allowlist, short refresh lifetimes, and a named owner.  Go to Q4.

Q4. Is this action high risk?
    (moves money, sends anything externally as the person, changes production,
     touches regulated data, or cannot be undone)
    YES ->  Add Pattern E: the person approves this specific action, and the
            agent receives a credential limited to it.
    NO  ->  Proceed with the pattern chosen above.

Q5. Will this agent hand work to another agent?
    YES ->  Apply the agent-to-agent rules below at every hop.
    NO  ->  Done.  Record the pattern in the agent registry.
```

### Agent-to-agent delegation rules

When agent one calls agent two, the delegation problem repeats, and every hop is a chance to escalate. Six rules:

1. **Downscope only.** Each hop gets the same or fewer rights than the hop before it. An agent can never grant what it was not given, and no agent ever holds more than the original person.
2. **Re-exchange at every hop.** Each agent gets its own token for its own audience. Do not forward tokens. The Model Context Protocol authorization specification explicitly forbids MCP servers from passing through the token they received.
3. **Bind every token to its audience.** Use resource indicators (RFC 8707) so a token minted for one service is rejected by another.
4. **Carry the whole chain.** RFC 8693 allows nested `act` claims to represent a chain of delegation. Note that the RFC treats only the current actor as relevant for access decisions and earlier actors as informational, so if you want policy that depends on the chain, your policy engine has to read it explicitly.
5. **Cap the depth.** Set a default maximum chain length (two or three hops is a sensible starting point). Anything deeper needs a design review.
6. **Keep the person as the subject.** However many agents are involved, the original human stays the subject of every token in the chain, so attribution survives.

---

## How to apply it

### Worked examples

**1. The nightly metrics agent.** It summarizes support ticket trends every night and posts them to a channel. No person is asking (Q1: no), so it uses **Pattern A**: its own identity, read-only access to the ticketing API, and posting rights to one channel. Nothing about it should involve a user's token.

**2. The executive assistant: "Book my trip to the board meeting and expense it."**
- Reading her calendar and email to find dates and preferences: a person is asking, and those systems trust the enterprise authorization server (Q2: yes), so **Pattern B**. The agent gets a token that names her as the subject and itself as the actor, limited to read access for this task.
- Booking with the travel provider: third party (Q2: no). If both apps support enterprise-managed authorization, **Pattern C**; otherwise **Pattern D** behind admin approval.
- Paying and submitting the expense: money moves (Q4: yes), so **Pattern E**. She approves the specific booking and amount, and the agent receives a credential limited to that action, ideally with the amount ceiling expressed in `authorization_details`. A backchannel approval flow such as OpenID CIBA lets her approve on her phone without being in the agent's session.

**3. The coding agent and its helper.** A developer asks a coding agent to fix a bug and open a pull request. The coding agent calls a test-runner agent. The developer is the subject throughout (Pattern B). The test runner gets a re-exchanged, downscoped token: read the repository and run tests, but not merge. The chain depth is two, within the default cap.

### Rolling it out

1. **Classify your top 20 agents** (one to two weeks). For each, record which pattern it uses today and which the tree says it should use. The gap list is your roadmap.
2. **Remove impersonation and shared tokens for person-present agents** (first quarter). These are the two patterns the tree never selects.
3. **Add step-up to high-risk actions** (next). Start with payments and outbound communication sent as a person.
4. **Make the log line complete** (in parallel). See below.

### What every log line must show

| Field | Why |
|---|---|
| Subject (the person, or none for Pattern A) | Accountability |
| Actor (the agent's own identity) | Separates human and agent actions |
| Delegation chain (each hop) | Reconstructs agent-to-agent paths |
| Pattern used (A to E) | Lets you audit the decision, not just the action |
| Scope or authorization details | Shows what was allowed at that moment |
| Approval reference (for Pattern E) | Proves the person approved this specific action |
| Token identifier and audience | Ties the action to a revocable credential |

---

## Metrics

- **Attribution coverage:** percentage of agent actions where logs show both the person and the agent as separate identities.
- **Impersonation count:** person-present agents still running on a person's own session or token. Target: zero.
- **Shared token count:** agents serving many users through one powerful service credential. Target: zero for agents that act for people.
- **Step-up coverage:** percentage of high-risk agent actions that required a person's approval for that specific action.
- **Grant mix:** enterprise-managed authorization versus user-consented grants for third-party apps. The trend should move toward enterprise-managed as app support grows.
- **Maximum observed chain depth:** if it exceeds your cap, something was built without review.

---

## Tradeoffs and anti-patterns

- **Impersonation for convenience.** It is the fastest way to ship and the hardest to unwind. Logs lie, and the agent inherits everything.
- **One service token for everyone.** It moves authorization out of your identity stack and into the agent's code, where a prompt injection can talk its way past it.
- **Token passthrough.** Forwarding the token an agent received to the next service skips audience checks and erases the hop from the chain.
- **Consent once, forever.** A user-consented grant with a refresh token that never expires is standing access with a friendlier name.
- **Step-up on everything.** If people approve twenty prompts a day, they stop reading them. Reserve Pattern E for actions that matter.
- **"Full delegation" as a default.** Delegation should start from nothing and add what the task needs, not start from everything the person has.

**What I chose not to cover:** cross-organization delegation, where your agent acts in another company's systems, and signing or attestation of agent outputs. Both deserve their own treatment.

---

## What this means for product teams

- **Make the intersection easy.** Computing "this person's rights, this agent's purpose, this task" is the heart of Pattern B, and today it is mostly hand-built. An authorization server or policy engine that does this cleanly, with a readable explanation, is a real product.
- **Ship enterprise-managed authorization on both sides.** Pattern C only helps where the agent app and the target app both support it. SaaS vendors that accept ID-JAG style grants remove a consent screen for every enterprise customer.
- **Treat the log line as a feature.** Buyers will choose the product that writes subject, actor, and chain into downstream audit logs without custom work.
- **Design step-up for humans.** Approval prompts need to say plainly what will happen, for how much, and to whom. Good step-up design is a usability problem as much as a security one.

---

## Sources

- IETF RFC 8693, OAuth 2.0 Token Exchange (actor and may_act claims): https://datatracker.ietf.org/doc/rfc8693/
- IETF RFC 9396, OAuth 2.0 Rich Authorization Requests (May 2023): https://datatracker.ietf.org/doc/rfc9396/
- IETF RFC 8707, Resource Indicators for OAuth 2.0: https://datatracker.ietf.org/doc/rfc8707/
- IETF OAuth Working Group, Identity Assertion JWT Authorization Grant (Internet-Draft, not yet an RFC): https://datatracker.ietf.org/doc/draft-ietf-oauth-identity-assertion-authz-grant/
- Model Context Protocol, Authorization specification (2025-11-25): https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization
- OpenID Foundation, Client-Initiated Backchannel Authentication (CIBA) Core 1.0: https://openid.net/specs/openid-client-initiated-backchannel-authentication-core-1_0.html
- OWASP GenAI Security Project, Top 10 for Agentic Applications (December 2025): https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/

---

*Author: Santosh, security product leader focused on identity governance for the enterprise and for AI agents. Views are my own. Free to use and adapt with attribution.*

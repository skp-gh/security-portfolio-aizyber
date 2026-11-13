---
title: "Teardown: How the Major Identity Vendors Are Approaching Non-Human and Agent Identity"
series: Agentic Identity Notes #3
type: Teardown
date: 2026-11-10
tags: [agentic-ai, non-human-identity, vendor-analysis, iam, pam, iga, buyer-guide]
---

# Teardown: How the Major Identity Vendors Are Approaching Non-Human and Agent Identity

*A buyer's map of where Microsoft, Okta, CyberArk, SailPoint, Ping, and the cloud platforms are placing their bets on agent identity, what each approach is good at, and the questions to ask before you sign anything.*

**Series:** Agentic Identity Notes #3 · **Type:** Teardown · **Read time:** 11 minutes

---

## TL;DR

- Every major identity vendor now ships something for AI agents. They are not building the same thing. **Each one is approaching agents from the seat it already holds**: the directory, the vault, the governance platform, or the token and gateway layer.
- That starting point predicts the strengths and the blind spots. Directory-led products are strong on registration, ownership, and access policy. Privilege-led products are strong on standing access and session control. Governance-led products are strong on discovery, ownership, and certification. Delegation-led products are strong on runtime token exchange and MCP gateways.
- The standards layer is converging faster than most buyers realize. The IETF draft that underpins Cross App Access (ID-JAG) is co-authored by people from Okta and Ping, and it is the basis for MCP's enterprise-managed authorization extension.
- The biggest open gaps are the same across vendors: **agents that live outside your primary ecosystem, delegated OAuth grants held by third parties, per-action authorization, and attribution in downstream logs.** No single purchase closes all of them.

---

## The problem

If you are a CISO, you have probably sat through several "agent identity" briefings this year. Each one is convincing on its own. Put them side by side and they describe different problems, different architectures, and different places in your stack where the control lives.

That makes buying hard. It is easy to end up with three overlapping agent inventories, two kill switches that do not know about each other, and still no answer to the question from week one: *who owns this agent, and what is it allowed to do right now?*

This teardown is a map, not a ranking. It is based on public announcements and product documentation available as of early November 2026, checked against the sources at the end. It is not a hands-on test of any product, and features labeled preview or "planned" may have shipped or changed by the time you read this.

---

## The core idea: four starting points

The cleanest way to read the market is to ask where each vendor sat before agents showed up. That seat shapes everything.

| Starting point | Core belief about agents | Vendors leaning this way | Where the control lives |
|---|---|---|---|
| **Directory-led** | An agent is a new kind of account in the directory. Register it, give it a sponsor, apply access policy. | Microsoft (Entra Agent ID), Okta (Okta for AI Agents, Agent SSO) | The IdP: identity objects, conditional access, SSO-style app connections |
| **Privilege-led** | An agent is a privileged identity. Remove standing access and watch the session. | CyberArk (now part of Palo Alto Networks) | The vault and privilege layer: just-in-time access, zero standing privilege, session monitoring |
| **Governance-led** | An agent is an identity that must be discovered, owned, certified, and retired. | SailPoint (Agentic Fabric) | The governance platform: discovery, ownership rules, certifications, lifecycle |
| **Delegation-led** | An agent acts for someone. Get delegation right at runtime, token by token. | Ping Identity (Identity for AI) | Token exchange, authorization server, and an MCP-aware gateway |

The cloud platforms form a fifth group with a different goal. Google's Agent Identity gives agents running on its agent platform a SPIFFE-based workload identity, and AWS positions Bedrock AgentCore as handling authentication and access control for agents built there. These are strong for agents you build on that cloud and say little about the third-party agents your employees connect to SaaS.

Most vendors are reaching into neighboring seats (SailPoint now talks about runtime authorization, Okta ships discovery and kill switches, Ping covers detection). The starting point still tells you where the depth is.

---

## Vendor by vendor

### Microsoft: Entra Agent ID

**What it is.** Microsoft Learn now describes Entra Agent ID as generally available. Agents get their own identity objects, created from **agent identity blueprints** that hold credentials and act as a policy attach point. Agents have owners, sponsors, and managers as defined administrative relationships. Conditional Access applies to agents, with templates for blocking high-risk agent identities and for autonomous and on-behalf-of access. Entra ID Governance adds access packages and sponsor lifecycle workflows, including templates that transfer sponsorship when a sponsor leaves. Microsoft also documents patterns for agents running outside Microsoft (AWS, Google Cloud, n8n), and says agent registry experiences are converging under Microsoft Agent 365.

**Strengths.** The deepest integration of agent identity into an existing directory, conditional access, and governance stack. Sponsorship as a required relationship maps directly to "every agent has an owner."

**Watch for.** The richest controls work best for agents in the Microsoft ecosystem. Several agent security features appear to require add-on licensing beyond Entra ID P1 or P2; confirm the exact SKUs with Microsoft for your tenant rather than relying on third-party summaries. Some admin experiences were still labeled preview in the documentation.

### Okta: Okta for AI Agents and Agent SSO

**What it is.** Okta says Okta for AI Agents has been generally available since May 2026 as a separate subscription. In August 2026 it announced general availability of **Agent SSO**, which brings the open Cross App Access (XAA) standard into its core SSO product at no additional cost and registers XAA agents in Universal Directory, so admins assign and revoke agent access with the same workflows used for employees. At Oktane in September 2026 it announced agent-to-agent connections and resource access certifications as generally available, with shadow agent discovery for endpoints, an Agent Gateway, and runtime kill switch capabilities planned for later quarters.

**Strengths.** The clearest bet on an open standard. XAA lets the enterprise IdP, rather than each individual user, decide which agent can connect to which app. That directly addresses the consent-screen problem from week two. Okta reports more than 25 software makers have signed on to XAA.

**Watch for.** XAA only helps where both the agent and the target app support it. For everything else (legacy apps, apps that have not adopted the standard), you are back to classic OAuth grants and discovery. Several runtime and discovery features were announced as planned, not shipped, at the time of writing.

### CyberArk (Palo Alto Networks): Secure AI Agents

**What it is.** CyberArk announced general availability of its Secure AI Agents solution in November 2025. The announced capabilities include agent discovery across SaaS, cloud, and developer environments with ownership and access profiles; least-privilege access with **zero standing privileges** and activity auditing; lifecycle management from creation to decommission; and real-time threat detection. Palo Alto Networks completed its acquisition of CyberArk in February 2026 and positions the combined company around securing human, machine, and agent identities. It says CyberArk's identity security solutions remain available as a standalone platform.

**Strengths.** The privilege lens is the right instinct for agents that can change production, move money, or touch regulated data. Zero standing privilege and session monitoring are mature disciplines with years of operational practice behind them.

**Watch for.** Privilege tooling is strongest where agents use credentials it brokers. Third-party SaaS agents holding delegated OAuth tokens on a vendor's servers are a harder fit. Ask how the roadmap will integrate with the broader Palo Alto platform and what that means for customers who do not use it.

### SailPoint: Agentic Fabric

**What it is.** SailPoint announced general availability of **SailPoint Agentic Fabric** in August 2026. It uses endpoint and browser sensors to surface hidden AI agents, credentials, and MCP servers, applies automated ownership rules so every machine account has a human accountable for it, and provides a centralized kill switch. In October 2026 SailPoint added shadow AI discovery, runtime authorization, just-in-time provisioning for people, service accounts, and agents, and announced autonomous identity security posture management (A-ISPM), with some of those pieces expected in a later fiscal quarter.

**Strengths.** Governance is where agent programs fail first (no owner, no review, no retirement), and this is SailPoint's home ground. Endpoint and browser discovery is a practical answer to shadow agents that never touch your IdP.

**Watch for.** Governance platforms historically run on periodic cycles. Ask how quickly discovery reflects a new agent, and whether runtime authorization is enforced inline or evaluated after the fact. Confirm which capabilities are generally available versus rolling out.

### Ping Identity: Identity for AI

**What it is.** Ping launched Identity for AI in November 2025 and its release notes record general availability on March 31, 2026. It includes first-class agent identity, **delegation instead of impersonation** through OAuth 2.0 token exchange using actor claims, least-privilege access at runtime, an MCP-aware gateway built on PingGateway that validates delegation tokens and audits MCP traffic, and detection of computer-using agents in PingOne Protect.

**Strengths.** The most explicit focus on the "on behalf of" problem. Token exchange with an actor claim means downstream systems can see both the person and the agent, which is exactly what week two's repudiation row needs.

**Watch for.** Delegation done properly needs cooperation from resource servers that accept and log the exchanged tokens. The value grows with how much of your estate sits behind Ping's gateway or authorization server.

### The cloud platforms

Google's Agent Identity gives each agent on its agent runtime a SPIFFE-based identity that it can use to authenticate to tools, Google Cloud services, and other agents, either on its own authority or on behalf of a user. AWS positions Bedrock AgentCore as handling authentication and access control for agents built on it. For agents you build, these are often the right place to start, because the identity is issued by the platform that runs the code. They are not designed to govern the third-party agents your workforce connects to SaaS.

---

## The standards layer

This is the part most buyer conversations skip, and it may matter more than any single product.

- **Identity Assertion JWT Authorization Grant (ID-JAG).** An IETF OAuth working group draft (revision 06 at the time of writing, not yet an RFC) that lets an application use an identity assertion from the enterprise IdP to get an access token for a third-party API. It is informally called Cross-App Access. Its authors list affiliations with Okta and Ping Identity.
- **MCP authorization.** The Model Context Protocol authorization spec (2025-11-25 version) is built on the OAuth 2.1 draft plus several OAuth RFCs, and points to a separate repository of authorization extensions. Vendor sources describe an Enterprise-Managed Authorization extension based on ID-JAG that reached stable status in 2026.
- **OpenID Foundation work.** The OIDF published a whitepaper, *Identity Management for Agentic AI*, in October 2025, and in October 2026 its AI Identity Management community group published a protocol-agnostic threat classification for cross-boundary agent authorization.

Why it matters to a buyer: if your agent identity strategy depends on one vendor's proprietary connector model, you are betting against a standard that competitors are co-authoring. Ask every vendor where they stand on ID-JAG, token exchange (RFC 8693), and MCP authorization.

---

## Capability map

How the four starting points line up against the eight capabilities in the [Agentic Identity Maturity Model](../frameworks/agentic-identity-maturity-model.md). This is my read of where each approach has **depth** based on public material, not a scored product evaluation. Every vendor is extending into the other columns.

| Capability | Directory-led | Privilege-led | Governance-led | Delegation-led |
|---|---|---|---|---|
| Discovery and inventory | Agents registered in the directory; discovery of unregistered agents is newer | Discovery across SaaS, cloud, dev | **Deepest**: endpoint and browser sensors | Registration-centric |
| Ownership and accountability | **Deep**: sponsors and owners as directory relationships | Ownership in agent profiles | **Deep**: automated ownership rules | Present |
| Authentication and credentials | **Deep**: agent objects with managed credentials | **Deep**: vaulted, brokered, zero standing | Relies on connected systems | Secretless patterns, gateway-injected credentials |
| Authorization and scope | Conditional access, access packages | JIT and least privilege | JIT provisioning, runtime authorization (newer) | **Deepest** on delegation and token scope |
| Delegation (on behalf of) | OBO policy templates; XAA for app-to-app | Not the focus | Not the focus | **Deepest**: token exchange with actor claims |
| Monitoring and detection | Risk-based policies for agents | **Deep**: session monitoring, threat detection | Posture and anomaly detection | Agent detection, gateway audit |
| Revocation and kill switch | Disable agent or blueprint, revoke via directory | Revoke brokered access | Centralized kill switch | Gateway-level enforcement |
| Cross-ecosystem reach | Strongest inside its own ecosystem, extending via standards | Broad for credentials it brokers | Broad via sensors and connectors | Broad where traffic passes its gateway |

---

## How to apply it

### Step 1: Decide your system of record for agent identity (one week)

Before you evaluate anyone, decide which platform will hold the authoritative list of agents, their owners, and their risk tier. For most organizations that is either the workforce IdP or the governance platform. Everything else should read from it or feed it. If you skip this, you will buy overlapping inventories.

### Step 2: Map your agents to the four starting points (two weeks)

Sort your known agents into three buckets:

1. **Agents you build** on your own cloud or agent platform. Workload identity from the platform is usually the right base.
2. **Third-party agents connected to SaaS** through OAuth. This is the week two problem. Directory-led controls (XAA where supported) and discovery tools matter most.
3. **Agents with privileged reach** into production, finance, or regulated data. Privilege-led controls matter most, regardless of who built them.

The size of each bucket tells you which starting point should lead.

### Step 3: Ask every vendor the same ten questions

1. Which of your agent capabilities are generally available today, and which are preview or planned? Show me the documentation.
2. How do you discover an agent that never authenticates through our IdP?
3. Where is the owner or sponsor recorded, and what happens automatically when that person leaves?
4. Can an agent act on behalf of a user without holding that user's full rights? How is the delegation represented in the token?
5. Do downstream audit logs show the agent and the user as separate actors?
6. What is your position on ID-JAG, RFC 8693 token exchange, and MCP authorization?
7. How fast can we revoke every credential and grant for one agent across all connected systems? Show me the drill.
8. What does this cost beyond our current licenses, and which features sit in a separate SKU?
9. How do you handle agents built on other clouds and agent platforms?
10. What will you integrate with, rather than replace, in our existing IAM, PAM, and IGA stack?

### Step 4: Run a two-agent bake-off

Pick one third-party SaaS agent and one internally built agent with real permissions. Ask each shortlisted vendor to show discovery, ownership, scoping, logging, and revocation for both. Thirty minutes per agent tells you more than any slide.

---

## Metrics

- **Percentage of known agents recorded in the system of record** with an owner and risk tier
- **Number of distinct agent inventories** across your tools (target: one authoritative, the rest feeding it)
- **Percentage of agent-to-app connections using an enterprise-managed authorization path** (XAA or equivalent) rather than user consent
- **Percentage of privileged agents with zero standing access**
- **Time to revoke one agent everywhere**, measured in a drill, compared across vendors in your bake-off

---

## Tradeoffs and anti-patterns

- **Buying from the slide, not the docs.** Announcements in this market often bundle generally available, preview, and planned features in one sentence. Always ask for the documentation page.
- **Letting every tool keep its own agent list.** Three inventories with three different counts is worse than one imperfect list, because nobody trusts any of them.
- **Picking a vendor before picking a starting point.** If your biggest risk is third-party SaaS agents, a privilege-first purchase will not address it, however good the product is.
- **Ignoring licensing until procurement.** Several vendors place agent features in separate subscriptions or add-ons. Model the cost early.
- **Assuming the standard solves adoption.** Open standards only help where both sides implement them. Plan for a long tail of apps that will not support XAA for years.

What I chose not to cover: the specialist non-human identity startups, SSPM vendors, and AI security platforms that approach this from outside identity. They matter, especially for discovery, and deserve their own teardown.

---

## What this means for product teams

- **The integration layer is the prize.** Buyers do not want a fifth console. The vendor that becomes the agent system of record, and lets the others read and write to it, wins the account.
- **Delegation is the hardest problem and the least solved.** Token exchange and actor claims exist. Making them easy for SaaS vendors to accept and log is a product and ecosystem problem, not a protocol problem.
- **Discovery is becoming table stakes.** Every category is adding it. Differentiation moves to what you do after discovery: ownership, scoping, and fast revocation.
- **Honest availability labels are a competitive advantage.** Security buyers have been burned by roadmap selling. A vendor that publishes a clear GA, preview, and planned matrix earns trust faster.

---

## Sources

- Microsoft Learn, What's new in Microsoft Entra Agent ID: https://learn.microsoft.com/en-us/entra/agent-id/whats-new-agent-id
- Okta, "Okta brings first-class identity to AI agents with Agent SSO" (August 24, 2026): https://www.okta.com/newsroom/press-releases/okta-brings-first-class-identity-to-ai-agents-with-agent-sso/
- Okta, Oktane 2026 AI announcements (September 22, 2026): https://www.okta.com/newsroom/press-releases/ai-innovations-oktane-2026/
- SiliconANGLE, "Okta expands Cross App Access ecosystem" (June 23, 2026): https://siliconangle.com/2026/06/23/okta-expands-cross-app-access-ecosystem-secure-ai-agent-connections/
- Help Net Security, "CyberArk launches Secure AI Agents to safeguard privileged AI identities" (November 4, 2025): https://www.helpnetsecurity.com/2025/11/04/cyberark-secure-ai-agents-solution-2/
- Palo Alto Networks, "Palo Alto Networks Completes Acquisition of CyberArk" (February 11, 2026): https://www.paloaltonetworks.com/company/press/2026/palo-alto-networks-completes-acquisition-of-cyberark-to-secure-the-ai-era
- SailPoint, "SailPoint eliminates blind spots with unified protection across human, non-human, and AI agent identities" (August 4, 2026): https://investor.sailpoint.com/news-releases/news-release-details/sailpoint-eliminates-blind-spots-unified-protection-across-human
- SailPoint, "From Chaos to Control" (October 6, 2026): https://investor.sailpoint.com/news-releases/news-release-details/chaos-control-sailpoint-unlocks-secure-agentic-era-autonomous
- Ping Identity, Identity for AI launch (November 6, 2025): https://press.pingidentity.com/2025-11-06-Ping-Identity-Launches-Identity-for-AI-Solution-to-Power-Innovation-and-Trust-in-the-Agent-Economy
- Ping Identity, Identity for AI release notes: https://developer.pingidentity.com/identity-for-ai/release-notes/idai-whats-new.html
- Google Cloud, Agent Identity overview: https://docs.cloud.google.com/gemini-enterprise-agent-platform/govern/agent-identity-overview
- AWS, Amazon Bedrock AgentCore: https://aws.amazon.com/bedrock/agentcore/
- IETF, Identity Assertion JWT Authorization Grant (draft-ietf-oauth-identity-assertion-authz-grant): https://datatracker.ietf.org/doc/draft-ietf-oauth-identity-assertion-authz-grant/
- Model Context Protocol, Authorization specification (2025-11-25): https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization
- WorkOS, "MCP Enterprise-Managed Authorization and ID-JAG" (September 8, 2026): https://workos.com/blog/mcp-enterprise-managed-authorization-id-jag
- OpenID Foundation, "New whitepaper tackles AI agent identity challenges" (October 7, 2025): https://openid.net/new-whitepaper-tackles-ai-agent-identity-challenges/
- OpenID Foundation, "OIDF publishes threat model for cross-boundary AI agents" (October 2026): https://openid.net/oidf-publishes-threat-model-for-cross-boundary-ai-agents/

---

*Author: Santosh, security product leader focused on identity governance for the enterprise and for AI agents. Views are my own and based on public information; this is not a sponsored or vendor-reviewed analysis. Free to use and adapt with attribution.*

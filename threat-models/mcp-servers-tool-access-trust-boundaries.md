---
title: "Threat Model: MCP Servers and Tool Access, Where the Trust Boundaries Actually Are"
series: Agentic Identity Notes #7
type: Threat model
date: 2026-12-08
tags: [agentic-ai, mcp, threat-modeling, stride, non-human-identity, supply-chain]
---

# Threat Model: MCP Servers and Tool Access, Where the Trust Boundaries Actually Are

*A STRIDE walkthrough of the Model Context Protocol in the enterprise: who trusts whom, where tokens and instructions cross a line they shouldn't, and the controls that matter most.*

**Series:** Agentic Identity Notes #7 · **Type:** Threat model · **Read time:** 11 minutes

---

## TL;DR

- An MCP server is a privileged integration that an AI agent can call on its own. Teams install them the way they install a browser extension, and govern them about as well.
- There are more trust boundaries than most diagrams show. The one people miss: **tool names, descriptions, and outputs are untrusted input** that go straight into the model's context.
- Walking MCP through STRIDE, the highest risks are **Elevation of Privilege** (one broad service credential behind a server that many agents can reach), **Tampering** (tool poisoning and silent tool changes), and **Spoofing** (token passthrough and confused deputy flows).
- The controls that move the needle: an allowlist of approved servers with pinned versions, audience-bound tokens per server, no token passthrough, per-user consent instead of a shared service token, sandboxing for local servers, and logs that record which agent called which tool for which person.

---

## The problem

In week two I threat modeled the most common agent pattern: a third-party AI app holding a delegated OAuth token to a SaaS tool. MCP is the next layer down. It's how agents reach tools and data, and it's spreading fast because it makes integration easy.

Easy is the problem. A developer can add a community MCP server to a coding agent in a couple of minutes. A platform team can stand up an internal MCP server in front of the CRM in an afternoon, usually with one service token that has broad rights. Neither path goes through the review an equivalent integration would normally get.

The risks are already public:

- **Tool poisoning.** In April 2025, Invariant Labs described tool poisoning attacks, where hidden instructions inside an MCP tool's description steer the agent into actions the user never asked for, such as reading and leaking local files.
- **Client-side code execution.** CVE-2025-6514, disclosed by JFrog in July 2025, was an OS command injection in `mcp-remote`, a widely used proxy that connects MCP clients to remote servers. A malicious server could supply a crafted authorization endpoint and run commands on the client machine. It affected versions up to 0.1.15.
- **A rug pull in the supply chain.** In September 2025, researchers reported a malicious `postmark-mcp` package on npm. After a run of clean releases, a later version quietly added a hidden BCC that copied every email the agent sent to an outside address. Anyone on automatic updates got the change with no review.

None of these needed the model to fail. They needed someone to trust a server, a description, or an update that hadn't earned it.

The cost of ignoring this: every MCP server becomes a standing path from untrusted content to privileged action, with credentials that often outlive anyone's attention.

---

## The system being modeled

**Scenario:** A company lets employees use an AI assistant and a coding agent (the MCP hosts). Approved MCP servers include a remote internal server in front of the CRM and ticketing system, a vendor-hosted remote server for a SaaS tool, and a few local servers that run on developer laptops (file system, git, a community package from a public registry).

### Components

| # | Component | Owner | Notes |
|---|---|---|---|
| C1 | User | Your org | Asks the agent to do things. Approves (or ignores) consent and confirmation prompts. |
| C2 | MCP host and client (the agent app) | Agent vendor or your org | Runs the model, holds connections to servers, decides which tools to call. |
| C3 | LLM | Model provider | Reads tool names, descriptions, and results as part of its context. |
| C4 | Local MCP server | Package author | Runs on the user's machine with the user's OS privileges. |
| C5 | Remote MCP server (internal) | Your platform team | Fronts the CRM and ticketing APIs. Often holds a service credential. |
| C6 | Remote MCP server (vendor) | SaaS vendor or third party | Hosted outside your boundary. |
| C7 | MCP authorization server | Your IdP or the server operator | Issues tokens for remote servers. |
| C8 | Upstream APIs | SaaS vendors, internal teams | The systems that actually hold data and take actions. |
| C9 | Server registry or package source | Public registries, internal mirror | Where servers and their updates come from. |

### Data flow

1. The user adds or enables an MCP server in the host. For a local server, the host launches a process. For a remote server, it connects over HTTP.
2. For remote servers, the client runs an OAuth flow against the authorization server and gets an access token for that server.
3. The server advertises its tools: names, descriptions, input schemas. These go into the model's context.
4. The user asks for something. The model picks a tool and arguments. The client sends the call.
5. The server calls upstream APIs, using either a token issued for the user or its own service credential.
6. Results come back and go into the model's context, where they can influence the next tool call.
7. Logs, if any, are spread across the host, the server, and each upstream API.

### Trust boundaries

| Boundary | Between | Why it matters |
|---|---|---|
| **TB1** | User and the host | The user approves servers and actions with little context. Confirmation fatigue sets in fast. |
| **TB2** | Host and local server | A local server runs with the user's privileges. Installing one is running someone else's code on your laptop. |
| **TB3** | Host and remote server | Tokens cross here. Each server should get a token issued only for it. |
| **TB4** | Server metadata and the model's context | Tool descriptions and results are untrusted text that the model treats as guidance. This is where tool poisoning lives. |
| **TB5** | Remote server and upstream APIs | The server's credential decides the blast radius. A shared service token makes every user as powerful as the server. |
| **TB6** | Registry and your environment | Updates can change a tool's behavior with no review. |
| **TB7** | All of the above and your monitoring | If logs can't tie a tool call to an agent and a person, detection and forensics fail here. |

---

## STRIDE walkthrough

Likelihood (L) and Impact (I) are scored 1 to 3 for a typical enterprise that allows MCP use with light governance. Adjust for your environment.

| STRIDE | Threat | Where | L | I | Key controls |
|---|---|---|---|---|---|
| **Spoofing** | **Token passthrough:** a server accepts a token issued for something else and forwards it upstream, so the upstream API can't tell who is really calling. | TB3, TB5 | 2 | 3 | The MCP authorization spec forbids passthrough: servers must only accept tokens issued for them. Clients must use resource indicators (RFC 8707) so tokens are audience-bound. Test this in every server you approve. |
| **Spoofing** | **Confused deputy:** a proxy server uses a static client ID with a third-party API, so a malicious client can ride an earlier user consent to get a token for someone else. | TB3, C7 | 2 | 3 | Per-client consent at the proxy before forwarding to the third-party authorization server. Validate redirect URIs exactly. Follow the confused deputy guidance in the MCP security best practices. |
| **Spoofing** | Look-alike or typosquatted server in a public registry. | TB6 | 2 | 2 | Allowlist approved servers by publisher and package. Mirror them internally. Block unknown servers at the host where it's supported. |
| **Tampering** | **Tool poisoning:** hidden instructions in a tool description steer the model to read files, call other tools, or leak data. | TB4 | 3 | 3 | Review tool descriptions as code. Show users full descriptions. Treat tool metadata and results as untrusted input. Isolate high-risk tools in separate sessions. |
| **Tampering** | **Rug pull:** a server or package changes its tools or behavior after approval (the postmark-mcp pattern). | TB6 | 2 | 3 | Pin versions. Hash and diff tool definitions on every update, and re-approve on change. No automatic updates for servers with write access. |
| **Tampering** | Indirect prompt injection through tool results (a ticket, email, or web page) drives the next tool call. | TB4 | 3 | 2 | Human confirmation for write and send actions. Separate read and write servers. Limit which tools can be chained in one session. |
| **Repudiation** | A tool call shows up upstream as the server's service account, with no record of which agent or person asked for it. | TB5, TB7 | 3 | 2 | Pass user and agent identity through properly (token exchange, not passthrough). Log agent ID, user, tool, and arguments at the server. Send server logs to the SIEM. |
| **Information disclosure** | Over-broad server credential lets any connected user or agent read data they couldn't reach directly. | TB5 | 3 | 3 | Per-user authorization at the server. Scope minimization per tool. No "god token" behind a shared server. |
| **Information disclosure** | Session hijacking: a guessable or leaked session ID lets an attacker inject events or impersonate a session on a remote server. | TB3 | 1 | 2 | Secure, random session IDs bound to the user. Never use sessions for authentication. Verify every request. |
| **Information disclosure** | Server-side request forgery when a client fetches attacker-supplied URLs during discovery or auth. | TB3, C7 | 1 | 2 | Restrict which URLs clients will fetch. Block internal address ranges. Follow the SSRF guidance in the MCP security best practices. |
| **Denial of service** | A looping agent hammers a server or an upstream API, burning shared quotas. | C5, C8 | 2 | 1 | Rate limits per agent and per user at the server. Alert on spikes. |
| **Elevation of privilege** | **Local server code execution:** a local server (or the client tooling around it) runs commands with the user's privileges, as with CVE-2025-6514 in mcp-remote. | TB2 | 2 | 3 | Run local servers in a sandbox or container with minimal file and network access. Keep client tooling patched. Show users the exact command before a local server is launched. |
| **Elevation of privilege** | Scope creep: a server asks for broad scopes at install "just in case," and every tool inherits them. | TB3, TB5 | 3 | 2 | Start with minimal scopes and request more only when a tool needs them (step-up). Treat a scope increase like a privilege escalation request. |

### The top five, ranked

1. **One broad credential behind a shared server** (EoP, Information disclosure). If the server can do everything, so can every agent that can reach it. This is the MCP version of week two's over-scoped OAuth grant.
2. **Tool descriptions and results treated as trusted** (Tampering). Tool poisoning and injection through results are the threats where the defenses are least mature. OWASP's Top 10 for Agentic Applications (December 2025) lists tool misuse and agentic supply chain weaknesses among its ten risks for a reason.
3. **Unpinned servers and silent updates** (Tampering). Approving a server once means nothing if the next version can behave differently.
4. **Token handling at the server** (Spoofing). Passthrough and confused deputy flows break the chain of who is acting for whom. The spec rules are clear. Plenty of servers still need checking against them.
5. **Local servers with full user privileges** (EoP). A local MCP server is running third-party code on a laptop that holds source code, cloud credentials, and browser sessions.

---

## How to apply it

### Run this model on your MCP footprint (one day)

1. **Find the servers.** Check agent and IDE config files on managed devices, internal MCP server deployments, and network logs for MCP traffic to outside hosts. You'll likely find more local servers than remote ones.
2. **Classify each one:** local or remote, internal or third-party, read-only or able to write, and what credential sits behind it.
3. **For each remote server, answer five questions:**
   - Does it only accept tokens issued for it, and does it reject passthrough?
   - Does it act with the user's authorization, or with its own service credential?
   - What is the broadest thing one tool call can do upstream?
   - Is the version pinned, and would we notice if a tool description changed?
   - Could we reconstruct who asked for a given tool call last Tuesday?
4. **For each local server,** check where it came from, whether it's pinned, and what it can reach on the machine.
5. **Score the STRIDE rows** for your top five servers. Anything scoring 3 on both L and I goes on the remediation list.

### Controls by maturity level

This maps to the [Agentic Identity Maturity Model](../frameworks/agentic-identity-maturity-model.md).

| Level | Controls for MCP servers and tool access |
|---|---|
| **L1** | Inventory of MCP servers in use, local and remote. Each has a named owner. |
| **L2** | Allowlist of approved servers with pinned versions. Remote servers use audience-bound tokens and reject passthrough. No shared god tokens: each server's credential is scoped to its tools. Server logs flow to the SIEM with agent and user identity. |
| **L3** | Per-user authorization at internal servers. Tool definitions are hashed and re-approved on change. Local servers run sandboxed. Write and send tools require human confirmation. Detections for unusual tool chaining and data volume. |
| **L4** | Each tool call is evaluated against policy (agent, user, tool, data sensitivity) at a gateway in front of MCP servers. Tokens are exchanged per call for the narrowest audience. Suspicious servers are disabled automatically across hosts. |

---

## Metrics

- **Percentage of MCP servers in use that are on the approved list** (target: 100% for anything with write access)
- **Percentage of approved servers with pinned versions and tool-definition change detection**
- **Number of remote servers backed by a shared service credential**, trending to zero
- **Percentage of tool calls where the server log records both the agent and the person**
- **Time to disable one MCP server across every host**, measured in a drill

---

## Tradeoffs and anti-patterns

- **Banning MCP outright.** Developers will run servers locally anyway, where you can see even less. Give them an approved list and a fast way to get a server added.
- **Reviewing the server once.** The postmark-mcp case shows why approval has to be tied to a version, not a name.
- **Trusting the gateway alone.** A gateway in front of MCP servers helps with policy and logging, but it can't fix a server that holds a god token upstream. Fix the credential first.
- **Relying on user confirmation for everything.** People click through prompts. Save confirmation for writes, sends, and sensitive reads, and make those prompts specific.
- **Forgetting the client side.** CVE-2025-6514 was in the tooling that connects clients to servers, not in a server. Patch and inventory the client tooling too.

What I chose not to cover: agent-to-agent protocols, and the general prompt injection problem beyond MCP. Both deserve their own pieces. Next week covers how delegation should work when an agent acts for someone.

---

## What this means for product teams

- **MCP server builders** should treat per-user authorization and audience-bound tokens as the default, publish tool definitions with versions and hashes, and make scopes small by default.
- **Agent and IDE vendors** are best placed to fix supply chain risk: managed allowlists, version pinning, visible diffs when a tool description changes, and sandboxed local servers out of the box.
- **Identity vendors** have a clear opening: MCP-aware gateways and authorization servers that issue narrow, exchanged tokens per tool call and record the full chain of who acted for whom.
- **The open gap** is runtime policy that understands tools: deciding, per call, whether this agent acting for this person should use this tool with these arguments right now. The building blocks (OAuth 2.1, RFC 8707, RFC 8693) exist. Making them easy is the product problem.

---

## Sources

- Model Context Protocol, Authorization specification (2025-11-25): https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization
- Model Context Protocol, Security Best Practices: https://modelcontextprotocol.io/specification/latest/basic/security_best_practices
- Invariant Labs, "MCP Security Notification: Tool Poisoning Attacks" (April 2025): https://invariantlabs.ai/blog/mcp-security-notification
- JFrog, "Critical RCE Vulnerability in mcp-remote: CVE-2025-6514 Threatens LLM Clients" (July 2025): https://jfrog.com/blog/2025-6514-critical-mcp-remote-rce-vulnerability/
- NIST NVD, CVE-2025-6514: https://nvd.nist.gov/vuln/detail/CVE-2025-6514
- The Hacker News, report on the first malicious MCP server found on npm, postmark-mcp (September 2025): https://thehackernews.com/2025/09/first-malicious-mcp-server-found.html
- The Register, "Postmark MCP server code hijacked" (September 29, 2025): https://www.theregister.com/2025/09/29/postmark_mcp_server_code_hijacked/
- OWASP GenAI Security Project, Top 10 for Agentic Applications (December 9, 2025): https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/
- IETF RFC 8707, Resource Indicators for OAuth 2.0: https://datatracker.ietf.org/doc/rfc8707/
- IETF RFC 8693, OAuth 2.0 Token Exchange: https://datatracker.ietf.org/doc/rfc8693/

---

*Author: Santosh, security product leader focused on identity governance for the enterprise and for AI agents. Views are my own. Free to use and adapt with attribution.*

---
week: 7
type: LinkedIn article
publish_on: Tue 2026-12-08, 8:30am PT
companion_artifact: threat-models/mcp-servers-tool-access-trust-boundaries.md
status: draft
---

# Your MCP Server Is a Privileged Account. Treat It Like One.

*Subtitle: Where the trust boundaries in MCP actually sit, and the few controls that matter most*

---

Here's a question worth asking your engineering leads this week: how many MCP servers are your agents connected to right now?

Not how many you approved. How many are running.

If the answer is a shrug, you're in good company. MCP, the Model Context Protocol, has become the standard way for AI agents to reach tools and data. It's spreading because it makes integration easy. That's also exactly why it deserves a hard look.

## Two very normal afternoons

Picture a developer who wants her coding agent to do more. She finds a community MCP server for the issue tracker in a public registry, adds three lines to a config file, and it works. The server runs on her laptop, with her permissions, next to her source code, her cloud credentials, and her browser sessions. It updates itself whenever the author ships a new version.

Now picture the platform team down the hall. They build an internal MCP server in front of the CRM and the ticketing system so every agent in the company can use it. To get it working quickly, they give the server one service token with broad rights. Any agent that can reach the server can now do whatever that token can do, for whoever happens to be asking.

Neither of these people did anything reckless. Both shipped something useful in an afternoon. And both created a privileged integration that skipped the review an equivalent integration would normally get.

## This has already gone wrong in public

It's tempting to file MCP risk under "theoretical." 2025 made that hard.

In April, Invariant Labs described **tool poisoning**: hidden instructions inside an MCP tool's description that steer the agent into actions the user never asked for, like reading local files and sending them somewhere. The model doesn't have to be broken. It just has to read the description, which is its job.

In July, JFrog disclosed CVE-2025-6514, a command injection flaw in `mcp-remote`, a popular tool that connects MCP clients to remote servers. A malicious server could run commands on the machine of anyone who connected to it.

In September, researchers reported a malicious `postmark-mcp` package on npm. After a run of normal releases, a later version quietly started copying every email the agent sent to an outside address. Anyone on automatic updates got the change without a single review.

None of these needed a clever attack on the AI. They needed someone to trust a server, a description, or an update that hadn't earned it.

## Where the trust boundaries actually are

Back in week two, I threat modeled an AI app holding a delegated OAuth token to your SaaS tools. MCP is the layer underneath that, and it has more boundaries than most diagrams show. The ones that matter most:

**Between your laptop and a local server.** Installing a local MCP server is running someone else's code with your privileges. We'd never let a random binary from the internet run on a developer machine with access to production keys. With MCP, we often do it through a config file.

**Between the agent and a remote server.** This is where tokens cross. The MCP authorization spec is clear here: clients have to request tokens bound to the specific server, and servers must not pass a token they received through to some other API. Token passthrough breaks the chain of who is acting for whom. The spec forbids it. Plenty of servers still need checking.

**Between the server and the systems behind it.** The credential the server holds decides the blast radius. A shared service token means every user and every agent is as powerful as the server. This is the MCP version of the over-scoped OAuth grant, and it's the one I'd fix first.

**Between tool metadata and the model.** This is the boundary people miss. Tool names, descriptions, and results all land in the model's context as text, and the model treats them as guidance. That makes them **untrusted input**, the same as an email from a stranger. Most teams review the code of a server, if they review anything, and never read the descriptions.

## The practical move

If I had to pick the unglamorous things that cut the most risk, they'd be these.

First, **know what's running.** Check agent and IDE config files on managed devices and look for MCP traffic to outside hosts. Expect to find more local servers than anyone thinks.

Second, **approve versions, not names.** Keep an allowlist of servers, pin the versions, and re-approve when a tool definition changes. The postmark-mcp case is the argument: approval of last month's package says nothing about this month's.

Third, **kill the god token.** Internal MCP servers should act with the user's own authorization, scoped per tool, not with one broad service credential. If a tool only needs to read tickets, its token should only read tickets.

Fourth, **sandbox local servers** with minimal file and network access, and keep the client tooling patched. CVE-2025-6514 was in the plumbing that connects clients to servers, not in a server.

None of this is exciting. It's the same discipline we already apply to service accounts and third-party integrations, finally applied to the thing agents use to touch everything else.

One honest aside: tool poisoning and injection through tool results are where I'm least confident the industry has good answers yet. Human confirmation for writes and sends, and keeping read and write tools apart, are the best defaults available today. I'd love to be proven wrong on that soon.

## Questions for your team

- Can we list every MCP server our agents use, local and remote, and who owns each one?
- Which of our internal MCP servers run on a shared service token, and what's the broadest thing one tool call could do?
- Would we notice if a server's tool description changed after we approved it?
- If one of these servers turned malicious tomorrow, how long would it take to disable it on every machine?

Next week I'm getting into the question that sits under all of this: **on-behalf-of done right**, a delegation decision tree for when an agent should act as itself, act with a slice of a person's rights, or not act at all.

Until then, I'm curious about one thing. If you've looked at MCP in your environment, what surprised you more: how many servers were running, or what credentials were behind them? Tell me in the comments.

---

*Share text for the publish dialog: see linkedin/week-07.md*

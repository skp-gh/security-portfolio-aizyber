---
week: 4
type: LinkedIn article
publish_on: Tue 2026-11-17, 8:30am PT
companion_artifact: prds/agent-credential-lifecycle-management.md
status: draft
---

# You Can't Revoke What You Never Wrote Down

*Subtitle: A PRD for the agent credential lifecycle, and why the off switch is the requirement to build first*

---

Here's a drill I'd love every security team to run once before the year ends.

Pick one AI agent. Not a toy. One that's connected to a few real systems and has been running for a while. Now cut it off completely: every key, every token, every OAuth grant, everywhere. Start a timer.

Then answer two questions. How long did it take? And how sure are you that you got all of it?

I suspect most teams would be surprised by both answers. I'd include myself in that. Revocation is the step everyone assumes is easy, right up until the day they have to do it under pressure.

## A Thursday afternoon email

Picture a security team at a company that's been fairly sensible about AI. They approved a third-party AI assistant for the sales and support teams. It's connected to the CRM and the ticketing system. Procurement did a review. Nothing reckless.

Then a Thursday afternoon email arrives from the vendor. They've had a security incident. Customers should revoke and rotate any credentials connected to the product.

So the team starts looking. They find OAuth grants under three different employee accounts, because three different people connected it on three different days. One of those people left in the spring. They find an API key in the vault, which is good, and the same key in a CI pipeline variable and a wiki page from the original setup, which is less good. They find a service account the vendor's onboarding guide told someone to create, with a password nobody has rotated since.

By Friday night they think they've got everything. Nobody would bet their job on it.

This isn't a far-fetched story. In August 2025, attackers used stolen OAuth and refresh tokens tied to the Drift AI chat agent to pull data out of customer Salesforce instances. Salesforce invalidated the active tokens and pulled the app from AppExchange, and customers were told to revoke and rotate. The containment step was revocation. The hard part, for a lot of organizations, was knowing what existed to revoke.

## Why this keeps happening

In identity, we've built good tools for each piece of this. Directories know about identities. Vaults know about secrets. SaaS admin consoles know about OAuth grants. PAM tools know about privileged sessions.

What almost nothing knows is the link between them. Which credentials belong to *this* agent? Who owns it today? And the question that bites hardest: **who actually holds each credential?**

That last one matters more than it sounds. If a refresh token sits on a vendor's servers, you can't rotate it. You can only revoke it at the source, and only if you know it's there. A lot of programs track where a secret is stored when it's in *their* vault, and have no field at all for "this one lives with someone else."

There's an operational trap too. Rotation policies are easy to write and hard to live with. The first rotation that takes down a production integration is very often the last one anyone schedules, and the policy quietly becomes a document nobody follows.

## So I wrote it as a PRD

This week's artifact is a product requirements doc for agent credential lifecycle management. I wrote it as a PRD on purpose, because I wanted something a security leader could hand to an internal platform team or to a vendor and get comparable answers back.

The lifecycle has four stages, and each one gets real requirements.

**Issue.** No registration, no credential. An agent has to exist in your system of record with an owner, a purpose, and a risk tier before it gets anything. And the governed path has to be fast, because if getting a proper credential takes a ticket and a week, engineers will reach for a personal token. I would too.

**Scope.** The agent declares what it's for when it's registered, and its credentials can't exceed that without approval. When it acts on someone's behalf, the token should carry both identities and never more than the person's own rights.

**Rotate.** On a schedule set by risk tier, and also on events: the owner changes jobs, the agent's model or tool set changes, or a vendor that holds the credential has an incident. Rotation has to be zero-downtime, or it won't survive the first outage.

**Revoke.** This is the one I'd build first, even though it happens last.

## The off switch is the whole point

The core requirement in the PRD is something I've called **revoke everywhere**. One action, for one agent, that revokes every credential and grant across every connected system, disables the agent's identity, and reports the result system by system.

Two details make it real rather than aspirational.

First, **verify it**. Revocation calls fail, time out, or get skipped for the system nobody remembered. After you revoke, test that each credential is actually dead. Don't assume it.

Second, add a **holder mode**. When a vendor emails you on a Thursday afternoon, you don't want to go agent by agent. You want to say "revoke everything this third party holds" and have it done.

The PRD also suggests time-to-revoke targets by tier. For the highest-risk agents, the ones that move money or change production, I've proposed 15 minutes. That's a starting point, not a standard. The real value is writing a number down and measuring against it.

And the long-term fix is having less to revoke. Short-lived tokens issued at runtime, bound to a specific resource, expire on their own. The standards for this already exist: token exchange, resource indicators (which the MCP authorization spec requires clients to use), and sender-constrained tokens. The gap is making them the default instead of the expert option.

## Start with the drill

If you do one thing after reading this, run the drill from the top of the article. One agent, a timer, and a notepad.

Write down every system you had to touch, how long each took, and which ones needed a human to log in and click something. That list is your revoke-everywhere runbook, version one. It's also a far more honest view of your agent credential risk than any dashboard.

Then do it for your ten most connected agents. It's unglamorous work. It's also the work you'll be grateful for at 4pm on a Thursday.

## Questions for your team

- For our most connected agent, can we list every credential it uses and who holds each one?
- How many agent credentials are held by third parties rather than by us?
- When did we last rotate an agent credential and confirm nothing broke?
- If a vendor disclosed an incident today, how long until every credential they hold is revoked and verified?

## The full PRD

The full document has personas, P0 to P2 requirements for each stage, lifetimes and revocation targets by risk tier, a 90-day rollout, metrics, open questions, and five questions to ask any vendor:

https://skp-gh.github.io/security-portfolio-aizyber/prds/agent-credential-lifecycle-management

Next week I'm turning to measurement: ten KPIs for an agent identity program that a CFO and a board will actually understand. Time to revoke will be one of them.

Until then, I'm curious. If you ran the drill tomorrow on your most connected agent, how long do you think it would take, and which system would be the last to let go? Tell me in the comments.

---

*Share text for the publish dialog: see linkedin/week-04.md*

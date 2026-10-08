---
week: 1
type: LinkedIn article
publish_on: Thu 2026-10-15, 8:30am PT
companion_artifact: frameworks/agentic-identity-maturity-model.md
status: draft
---

# Nobody Owns Your AI Agents. That's the Real Problem.

*Subtitle: Why agent security starts with a boring question, and a five-level model for answering it*

---

Here's a question I've started asking security leaders, and I've gotten used to the pause that follows:

"How many AI agents are running in your environment right now?"

Not how many AI tools you've approved. Not how many Copilot licenses you bought. Agents. Things that log in, call APIs, read data, and take actions without a human clicking each button.

Most people don't know. And honestly, a year ago I wouldn't have known either.

## Picture a normal Tuesday

A sales ops analyst wants to stop copying data between the CRM and a spreadsheet every morning. She finds an AI tool that can do it, connects it to the CRM with her own login, approves the OAuth prompt, and goes back to her day. It works great. She tells two teammates. They do the same thing.

Six months later she moves to a different team. The agent keeps running. It still has her permissions, which by now include a few things she picked up along the way. Nobody in security has ever seen it. It doesn't show up in the access review because, technically, it's her.

Nobody did anything malicious here. Everyone was trying to get work done. That's exactly why this is hard.

## We built identity for two kinds of things

I've spent more than 15 years in identity: IAM, PAM, IGA, customer identity, all of it. And almost everything we built rests on two assumptions.

The first is that **humans** are slow, interactive, and accountable. So we give them SSO, MFA, joiner-mover-leaver processes, and quarterly access reviews.

The second is that **workloads** are predictable. A service account does the same thing every day, so we put its password in a vault and move on.

AI agents don't fit either box. They act like users. They authenticate like services. And they change their behavior like neither. An agent can pick up a new tool tomorrow, get a new prompt, or start chaining actions nobody designed it to chain. Same credentials, completely different blast radius.

When I look at where agent risk actually shows up, it's rarely "the model said something wrong." It's that the agent was *allowed* to do too much, with nobody watching and nobody accountable.

## So I tried to put some structure around it

I wanted something simple enough to use in a board conversation but specific enough that an IAM team could act on it on Monday. Here's where I landed: five levels.

**Level 0, Invisible.** Agents exist. Security can't count them.

**Level 1, Inventoried.** You can produce a list, but most agents run on a person's credentials or a long-lived API key sitting in a config file.

**Level 2, Governed.** Every agent has its own identity, a named owner who's still in that role, and credentials that are scoped and expire.

**Level 3, Least privilege by default.** Agents get access per task, not forever. Acting "on behalf of" a person means getting a slice of their rights, never the whole thing. You know what normal looks like for each agent and you notice when it changes.

**Level 4, Continuously authorized.** Every action an agent takes is checked against policy in real time, and you can shut one down in seconds without waking anyone up.

If I'm being honest, most organizations I talk to are at 0 or 1. That's not a criticism. Most of this didn't exist two years ago.

## The unglamorous move that matters most

It's tempting to jump straight to Level 4. Real-time policy engines and agent-to-agent trust are genuinely interesting problems, and that's where a lot of the vendor energy is going.

But the biggest drop in risk for the least money is going from Level 1 to Level 2. It comes down to three things:

1. **Every agent gets an owner.** A real person, tied to a role, who gets asked about it when they change jobs. Agents with no owner get suspended.
2. **No agent runs on a human's credentials.** Give it its own identity, even if that's just a dedicated service account to start.
3. **Credentials expire.** Vault them, rotate them, and scope them to what the agent actually does.

None of this needs a new platform. It needs a decision that agents are identities, and the discipline to treat them that way. I'd frame it as a 90-day effort: a month to find them, a month to assign owners and migrate credentials, a month to tighten scopes and get agent activity into your SIEM.

## What I'd ask your team this week

If you want a quick gut check, try these four questions:

- Can we produce a list of the AI agents and AI-connected apps in our environment today?
- Does every one of them have an owner who still works in the same role?
- Are any of them running on a person's login?
- If one of them did something it shouldn't, could we tell whose behalf it was acting on?

If the answer to any of those is "I'm not sure," you've found your starting point.

## The full framework

I've published the complete model, with an eight-capability scoring matrix, a self-assessment, the 90-day plan, and the metrics I'd take to a board, on my GitHub. It's free to use and adapt:

https://skp-gh.github.io/security-portfolio-aizyber/frameworks/agentic-identity-maturity-model

This is the first piece in a weekly series I'm calling Agentic Identity Notes. Next week I'm walking through a threat model for an AI agent with delegated OAuth access to your SaaS apps, which is exactly the situation our sales ops analyst created.

I'm curious where you'd honestly put your organization on this scale, and which of the four questions above was hardest to answer. Tell me in the comments. I read all of them.

---

*Hashtags for the share post:* #IdentitySecurity #AgenticAI #CISO #AIGovernance #ZeroTrust

---
week: 8
type: LinkedIn article
publish_on: Tue 2026-12-15, 8:30am PT
companion_artifact: frameworks/on-behalf-of-delegation-decision-tree.md
status: draft
---

# Your AI Assistant Shouldn't Be You

*Subtitle: A simple decision tree for agent access, and why "on behalf of" should mean a slice of your rights, never a copy*

---

Here's a sentence every executive assistant agent will hear sooner or later:

"Book my trip to the board meeting and expense it."

It sounds like one request. From an identity point of view, it's at least four. Read her calendar. Read her email for the hotel preferences she always forgets to write down. Book with a travel provider that lives entirely outside your company. And move money.

Each of those deserves a different kind of access. In most deployments today, they all get the same one: the agent simply becomes her.

## The shortcut that works until it doesn't

Picture how this usually gets built. A team wants the assistant live before the quarter ends. The fastest path is to let the agent run on the executive's own session or token. Everything she can do, it can do. It works on the first try, the demo goes well, and everyone moves on.

Now look at it from the other side. Her calendar, her email, her expense approvals, maybe a few finance dashboards she can see because of her role. The agent holds all of it, all the time, for a task that needed a sliver.

And the logs? They say she did it. Every booking, every email it read, every expense it filed. If something goes wrong, say a prompt injection hidden in a calendar invite tells the agent to forward a board deck somewhere it shouldn't go, the audit trail points straight at her.

The other common shortcut is the opposite: one powerful service account that serves every user, with the agent's own code deciding who's allowed to see what. That moves your authorization logic out of the identity stack and into a system that can be talked into things. It's a confused deputy with a friendly chat interface.

Neither shortcut is malicious. Both are what happens when "on behalf of" is treated as a vibe instead of a design decision.

## What "on behalf of" should actually mean

In identity, delegation has always had a simple rule underneath it: the person acting for you gets a portion of your authority, for a purpose, and everyone can tell who did what.

For agents, that rule turns into three properties:

**It's a slice, not a copy.** The agent's access is the overlap between what the person can do, what the agent is for, and what this specific task needs. Usually that overlap is small.

**It's visible.** The token, and every log line downstream, shows two identities: the person and the agent. OAuth already has a standard way to say this. Token exchange (RFC 8693) lets a token carry the person as the subject and the agent as the actor.

**It's revocable on its own.** You can stop the agent without locking the person out of their own email.

None of this is exotic. The standards exist. What's been missing is a habit of asking the right questions before an agent goes live.

## Five questions instead of a debate

So this week I put it into a decision tree. Five questions, asked in order, for each kind of action an agent takes.

**Is a specific person asking for this right now?** If not, the agent should use its own identity, scoped to its job. A nightly report agent has no business holding anyone's token.

**Does the target resource trust your own authorization server?** If yes, exchange tokens so the agent gets a delegated slice with both identities attached.

**If it's a third-party app, do both sides support enterprise-managed authorization?** This is the IETF draft behind Cross App Access. When it's supported, your IdP decides by policy instead of each user clicking "Allow." When it isn't, you fall back to a user-consented grant, but behind admin approval, with tight scopes and short-lived refresh tokens.

**Is the action high risk?** Moving money, sending anything externally as the person, changing production. If so, the person approves that specific action, and the agent gets a credential limited to it. Not a blanket yes for the afternoon.

**Will this agent hand work to another agent?** Then every hop downscopes, gets its own token, and keeps the original person visible in the chain.

Run the travel request through that and it stops being one blob of access. Calendar and email get a read-only delegated token for this task. The travel booking goes through enterprise-managed authorization if the provider supports it. The payment waits for her to tap approve on her phone, for that booking and that amount.

## The agent-to-agent part is where it gets slippery

The last question deserves a bit more attention, because it's where good designs quietly go wrong.

Say a coding agent calls a test-runner agent, which calls a tool server. If each hop just forwards the token it received, you've built a privilege escalation path with no record of how it happened. The Model Context Protocol's authorization spec explicitly forbids MCP servers from passing tokens through, for exactly this reason.

The rules that keep chains sane are simple to say and take discipline to enforce. Each hop gets the same or fewer rights than the one before it. Each hop gets a fresh token bound to its own audience. The original person stays the subject the whole way down. And there's a cap on how deep the chain can go before someone has to review it.

One honest caveat: token exchange can carry a chain of actors, but the standard treats only the current actor as relevant for access decisions. If you want policy that depends on the whole chain, your policy engine has to read it on purpose. That's a detail that's easy to miss in a design review.

## Start with the log line

If you want one practical move from all this, it's an audit question you can ask about any agent today:

When this agent does something, does the log show the person, the agent, and the scope that was used?

If the answer is "it shows the person," you have impersonation. If it shows a service account, you have a shared token. Either way, you know which agents to redesign first.

It's a boring test. It's also the fastest way to find out whether "on behalf of" means anything in your environment.

## Questions for your team

- Which of our person-facing agents run on the person's own session or token today?
- Do any of our agents serve many users through one powerful service account?
- Which agent actions move money or send external messages, and do they require a specific approval?
- When one of our agents calls another, does the second one get a new, smaller token, or the same one?

Next week I'm stepping back from frameworks to look at what readers have pushed back on over the series so far, and what I'd rethink because of it.

Until then, here's the question I'd love answered in the comments. When an agent acts for one of your people today, does the token say "agent acting for this person," or does it just say it's the person?

---

*Share text for the publish dialog: see linkedin/week-08.md*

---
week: 5
type: LinkedIn article
publish_on: Tue 2026-11-24, 8:30am PT
companion_artifact: metrics/ten-kpis-for-an-agent-identity-program.md
status: draft
---

# Your Board Doesn't Want a Vulnerability Count

*Subtitle: Ten agent identity KPIs a CFO can actually read, and the one number to lead with*

---

Picture the quarterly board meeting. The CISO has twelve minutes. The slide deck is ready. Then, before slide two, a director leans forward and asks the question everyone has been reading about:

"Are we safe from these AI agents?"

What's on the slide? A vulnerability count. A phishing click rate. A list of tools. All true, all hard-won, and none of them answer the question.

That gap is what this week is about.

## The question behind the question

When a CFO or board member asks about AI agents, they're rarely asking about technology. They're asking three plain things.

How many of these do we have, and who's accountable for each one? What could one of them do if it went wrong? And if it did, how fast could we stop it?

Most security reporting wasn't built to answer those. It counts activity: alerts triaged, patches applied, tickets closed. Activity is real work, but it doesn't tell a board whether exposure is going up or down. A rising alert count could mean things are getting worse, or that detection got better. Nobody in the room can tell.

And the bar for board-level clarity keeps rising. The SEC's 2023 disclosure rules ask public companies to describe how the board oversees cybersecurity risk. NIST's Cybersecurity Framework 2.0 added a whole Govern function in 2024. Neither mentions AI agents specifically. But agents are quickly becoming one of the bigger, less visible parts of the risk those rules are about.

## Four questions a board can hold onto

So this week's artifact is a scorecard: ten KPIs, grouped under four questions that are simple enough to remember after the meeting ends.

**Do we know what we have?** Inventory coverage, owner coverage, and the number of orphaned agents whose owner left or changed roles without handing them off.

**Is each agent constrained?** How many run on their own identity instead of a person's login. How many high-risk agents still hold long-lived secrets. How many hold permissions they haven't used in a month. And how many of their credentials sit on a vendor's servers rather than yours.

**Can we stop one fast?** Time to revoke everywhere, and how often a revocation is actually verified rather than assumed.

**Can we prove what happened?** The share of agent actions logged with both the agent and the person it acted for.

Each KPI gets a formula, a data source, a starting target, and one sentence on why a CFO should care. That last part matters most. If you can't say why a number matters to the business in one sentence, it probably doesn't belong on a board slide.

## Lead with one number

If the board only remembers one metric, make it **time to revoke everywhere**.

Last week's piece on credential lifecycle ended with a promise that this one would make the list. Here's why it leads.

Most risk metrics are abstract. "Exposure" and "posture" mean different things to different people. Time to revoke is a duration. It answers, in minutes or hours, how long a compromised agent could keep operating before you shut it down across every system it touches. A CFO doesn't need a security background to understand the difference between 15 minutes and three days.

It's also honest. You get the number by running a drill: pick an agent, revoke everything, time it, and test that each credential is really dead. Either you can do it or you can't. There's no way to dress it up.

The suggested starting targets are 15 minutes for the highest-risk agents, an hour for the middle tier, and a day for low-risk, read-only ones. Those are starting points, not standards. The board should own the actual targets as a risk appetite decision.

## The vanity metric trap

One honest warning. The easiest numbers to report are often the least useful.

"We discovered 400 agents this quarter" sounds like progress. But if 380 of them still run on long-lived keys, the risk barely moved. Discovery counts go up whether you're getting safer or not.

The fix is simple: never report a count without a control rate next to it. Four hundred agents, 95% with a named owner, 70% on dedicated credentials, Tier 1 revocable in 20 minutes. That's a story a board can follow, and a trend they can hold you to.

Another trap is too many numbers. Ten KPIs is the most a working scorecard should carry, and the board slide should show fewer than that. Lead with one, support it with a handful, and put the methodology in an appendix nobody reads unless they want to.

## Start with five

You don't need all ten to start. In the first 30 days, most teams can measure five: inventory coverage, owner coverage, dedicated credential rate, over-scoped agents, and one revocation drill.

The first numbers will be imperfect. That's fine. The first quarter is a baseline, not a grade. What the board wants to see is the second quarter, when the numbers move.

Then tie every KPI to a decision. Fund discovery tooling. Enforce "no registration, no credential." Require vendors to document how their tokens get revoked. A metric without a decision attached turns into a vanity metric within a couple of quarters.

## Questions for your team

- If a director asked about AI agents at the next board meeting, what number would we show?
- Do we know our current time to revoke everywhere for our most connected agent?
- Which of our security metrics count activity rather than exposure?
- Who owns the targets: security, or the board?

Coming up next in the series, I'm going back to threat modeling: MCP servers and tool access, and where the trust boundaries actually are once agents start calling tools on your behalf.

Until then, I'm curious. If you could put only one AI agent metric in front of your board, which would you pick, and why that one?

---

*Share text for the publish dialog: see linkedin/week-05.md*

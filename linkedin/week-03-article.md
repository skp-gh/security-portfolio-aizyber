---
week: 3
type: LinkedIn article
publish_on: Tue 2026-11-10, 8:30am PT
companion_artifact: teardowns/how-identity-vendors-approach-agent-identity.md
status: draft
---

# Every Identity Vendor Sells Agent Security From Its Own Chair

*Subtitle: A buyer's map of how the big identity platforms are approaching AI agents, and the ten questions I'd ask before signing*

---

Here's a pattern I think a lot of security leaders will recognize this year.

You take five vendor meetings in a month. Every one of them has an "AI agent identity" slide. Every one of them is convincing. And when you try to line them up afterward, you realize they weren't describing the same problem.

One vendor talked about registering agents in the directory. Another talked about removing standing privilege. A third showed you a discovery dashboard with a kill switch. A fourth spent the whole hour on token exchange and gateways.

None of them were wrong. They were each answering the question from the seat they already sit in.

## A familiar kind of Tuesday

Picture a CISO at a mid-size company. She's read the headlines, she's seen the OAuth token incidents, and her board has asked the question every board is asking: "Are we on top of AI agents?"

So she does the sensible thing. She asks her IdP vendor, her PAM vendor, and her governance vendor what they've got. All three have an answer. All three answers include an agent inventory. Two include a kill switch.

Six months later she has three lists of agents with three different counts, two kill switches that don't know about each other, and still no clean answer to the question from week one of this series: who owns this agent, and what is it allowed to do right now?

Nobody made a bad decision here. Each purchase made sense on its own. That's what makes it a trap.

## The seat shapes the product

I've worked hands-on with several of these platforms over the years, so I'll say up front that I have my own habits and biases. With that caveat, here's the frame that helped me most when I went through what each vendor has actually published.

Ask where the vendor sat before agents showed up. That tells you where its depth is.

**Directory-led** vendors see an agent as a new kind of account. Microsoft's Entra Agent ID gives agents their own identity objects, with sponsors and owners as formal relationships and Conditional Access policies that apply to agents. Okta has gone hard on an open standard, Cross App Access, so the enterprise IdP decides which agent can connect to which app instead of every user clicking "Allow" on their own.

**Privilege-led** vendors see an agent as a privileged identity. CyberArk, now part of Palo Alto Networks, frames it around zero standing privilege, session monitoring, and threat detection. If an agent can touch production or move money, that's the right instinct.

**Governance-led** vendors see an agent as something to discover, assign an owner, certify, and retire. SailPoint's Agentic Fabric uses endpoint and browser sensors to find agents that never touch your IdP, and assigns human owners automatically.

**Delegation-led** vendors care most about the "on behalf of" problem. Ping's Identity for AI leans on OAuth token exchange so a token can show both the person and the agent acting for them, plus a gateway that sits in front of MCP servers.

Then there are the cloud platforms. Google and AWS give agents built on their platforms a workload identity. That's great for what you build. It says very little about the third-party agents your sales team connected to the CRM last quarter.

## Why the starting point matters more than the feature list

Every one of these vendors is reaching into the other columns. Directory vendors are adding discovery. Governance vendors are adding runtime authorization. That's healthy.

But depth takes years, and the seat a vendor started from still predicts where the product is strong and where it's thin. A privilege-first tool is excellent for agents using credentials it brokers. It's a harder fit for a third-party AI app holding a refresh token on its own servers, which was the whole point of last week's threat model. A directory-first tool is excellent inside its own ecosystem and has to work harder outside it.

So the useful question isn't "which vendor is best at agent identity?" It's "which of my agent problems is biggest, and which starting point fits it?"

One honest aside. Reading through announcements for this piece, I noticed how often generally available, preview, and planned features show up in the same paragraph. I don't think anyone's being sneaky. It's a fast-moving market. But it means you should ask for the documentation page, not the slide.

## The part nobody puts on the slide: standards

This is what surprised me most.

The IETF draft behind Cross App Access, called the Identity Assertion JWT Authorization Grant, is still a draft. But its authors list affiliations with both Okta and Ping, and it's the basis for an enterprise-managed authorization extension to MCP. The OpenID Foundation published a whitepaper on agent identity last October and a threat classification for cross-boundary agents just last month.

In other words, competitors are co-authoring the plumbing. If your agent strategy depends on one vendor's proprietary connector model, you're quietly betting against a standard that's picking up momentum.

## The practical move

Before you evaluate anyone, **pick your system of record for agent identity**. For most organizations that's either the workforce IdP or the governance platform. Everything else should read from it or feed it.

Then sort your agents into three rough buckets: agents you build, third-party agents connected to SaaS, and agents with privileged reach into production or regulated data. The biggest bucket tells you which starting point should lead.

It's boring. It's also the decision that keeps you from buying three overlapping inventories.

## Questions for your team

- Which platform is our system of record for agents today, if we're honest?
- How many separate agent lists exist across our tools right now?
- Of our known agents, how many are third-party SaaS connections versus things we built?
- Has anyone asked our vendors which agent features are GA versus preview?

## The full teardown

The full version walks through each vendor's published approach, a capability map against the maturity model from week one, ten questions to ask every vendor, and a two-agent bake-off you can run in an afternoon:

https://skp-gh.github.io/security-portfolio-aizyber/teardowns/how-identity-vendors-approach-agent-identity

Next week I'm getting specific about one piece of this: a PRD for agent credential lifecycle management, covering how credentials get issued, scoped, rotated, and revoked.

Until then, I'd love to hear one thing. If you've already bought something for agent identity, which seat did it come from, and was that where your biggest problem actually was? Tell me in the comments.

---

*Share text for the publish dialog: see linkedin/week-03.md*

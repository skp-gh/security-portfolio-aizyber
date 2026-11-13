---
week: 2
type: LinkedIn article
publish_on: Tue 2026-11-03, 8:30am PT
companion_artifact: threat-models/ai-agent-delegated-oauth-stride.md
status: draft
---

# Your AI Agent's Refresh Token Is the Real Perimeter

*Subtitle: A STRIDE walkthrough of the "Allow" button everyone clicks, and the five controls that matter most*

---

Last week I left you with a sales ops analyst who connected an AI tool to the CRM using her own login. The obvious pushback is fair: "OK, so what's the actual risk? She approved it. It's doing what she asked."

That's the right question. So this week I sat down and threat modeled her exact setup, one box and one arrow at a time.

The short version: the risk isn't the click. It's what the click leaves behind.

## What actually happens when someone clicks "Allow"

Here's the flow, minus the jargon.

The analyst clicks "Connect CRM" inside the AI tool. She gets bounced to the CRM's sign-in page, logs in through your SSO like always, and sees a consent screen listing what the tool wants to do. She clicks Allow. The CRM hands the AI vendor two things: a short-lived access token and a **refresh token**.

That refresh token is the part nobody thinks about. It lets the vendor's backend get new access tokens whenever it wants, for weeks or months, without her being there. It lives on the vendor's servers, not yours. Your SSO isn't involved again. Your conditional access policy checked her at login and then stepped out of the room.

Now add a few months. She changes teams. The vendor ships a new feature that reads email too, and she clicks Allow again without reading it. The tool starts summarizing customer notes that, it turns out, include an API key someone pasted into a support case two years ago.

Nothing here is malicious. Every step was reasonable. And you now have a standing, unmonitored path into your CRM and mailbox, held by a company you may never have reviewed.

## This isn't hypothetical anymore

It's easy to file this under "someday" risk. August 2025 made that harder.

That month, Google's threat intelligence team reported that an actor they track as UNC6395 had used stolen OAuth tokens from the Salesloft Drift integration, an AI chat product, to pull data out of many companies' Salesforce instances. According to Google's write-up, the actor ran count queries first, then exported records, then searched what they took for things like AWS keys and Snowflake tokens. A later update confirmed tokens for a Drift email integration were used to get into a small number of Google Workspace mailboxes.

The part I keep coming back to: Salesforce wasn't breached. Google Workspace wasn't breached. The **delegated tokens** were. The attacker didn't have to beat anyone's MFA, because the tokens had already been issued to a trusted app.

That's the identity lesson. We spent years hardening the front door, and this walked in through a door we'd propped open for convenience.

## Why I reached for STRIDE

STRIDE is old. Microsoft has been teaching it for a long time, and some people roll their eyes at it. I like it here precisely because it's boring. It forces you to ask six plain questions about every boundary instead of jumping straight to the scary AI stuff.

When I ran the analyst's setup through it, I ended up with five trust boundaries and about a dozen threats. A few stood out.

**Elevation of privilege** was the worst, and it wasn't close. The agent holds whatever the user delegated, which is usually far more than the use case needs. Every other threat on the list gets worse because of this one. If the token can only read three objects, a stolen token can only read three objects.

**Information disclosure** came next: someone replays a stolen refresh token from their own infrastructure and does a bulk export. That's the Drift pattern.

**Repudiation** surprised me a little in how often it shows up. In a lot of SaaS audit logs, the agent's actions look like the user's. So when something goes wrong, you can't tell whether the analyst did it or her AI tool did. You can't detect what you can't attribute.

And then there's **tampering via prompt injection**. An email or a CRM note contains instructions, the agent reads them as part of its job, and suddenly it's updating records or forwarding mail. I'll be honest: this is the row where I'm least confident the industry has good answers yet. Read-only by default and a human approval step for sensitive writes are the best I've got.

## The controls that matter (they're not exciting)

If you take one thing from the full model, take this list. None of it needs a new platform.

**Gate consent.** Turn off user consent for anything beyond basic profile scopes, and stand up an admin approval workflow that answers within a day. If approval takes three weeks, people will route around it with personal accounts.

**Allowlist scopes per app.** Decide what each approved AI app is allowed to ask for. Treat any request for new scopes like a privilege escalation, because that's what it is.

**Shorten and constrain tokens.** Set refresh token lifetimes and IP restrictions for third-party apps where your SaaS platforms support it. The current OAuth security best practice (RFC 9700) pushes toward sender-constrained tokens for exactly this reason.

**Make the agent visible in your logs.** Push your SaaS vendors for the app or client ID on every audit event, and get it into your SIEM as its own attribute.

**Practice revocation.** Pick your most widely used AI app and write down, today, what it would take to kill every token it holds across every tenant. Then time yourself doing it. I suspect a lot of teams will find this takes days, not minutes. I'd want to know that before an incident, not during one.

## Questions to take into Monday

- Who's allowed to consent to third-party apps in our CRM, mail, and file platforms today?
- Which AI apps hold full mailbox or full CRM scopes, and does the use case actually need them?
- If one of those vendors called us tomorrow with a breach notice, how long would it take to revoke every token?
- Can our SIEM tell the difference between a user and an app acting as that user?

## The full threat model

The complete version has the component and data flow breakdown, all five trust boundaries, a scored STRIDE table, a half-day exercise for running it on your own top five AI apps, and controls mapped to the maturity model from last week:

https://skp-gh.github.io/security-portfolio-aizyber/threat-models/ai-agent-delegated-oauth-stride

Next week I'm zooming out to the vendor side: how the major identity vendors are approaching non-human and agent identity, where they're strong, and where the gaps still are.

In the meantime, I'm curious about one thing. If you've ever had to revoke a third-party OAuth app across your whole tenant in a hurry, how long did it actually take, and what broke that you didn't expect? Tell me in the comments.

---

*Share text for the publish dialog: see linkedin/week-02.md*

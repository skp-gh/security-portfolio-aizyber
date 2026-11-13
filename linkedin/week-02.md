---
week: 2
type: Article share text ("Tell your network what your article is about")
publish_on: Tue 2026-11-03, 8:30am PT
article: linkedin/week-02-article.md
artifact: threat-models/ai-agent-delegated-oauth-stride.md
status: draft
---

# Share text, Week 2

Paste this into the "Tell your network what your article is about" box when you publish the article. LinkedIn attaches the article card automatically, so no link is needed here. The GitHub threat model link lives inside the article.

## Share text

When someone in your company clicks "Allow" on an AI app's OAuth screen, the risk isn't the click. It's the refresh token the click leaves behind.

That token lives on the vendor's servers, not yours. Your SSO checked the user once and stepped out of the room. The Salesloft Drift campaign Google reported in 2025 showed what happens next: nobody's MFA was beaten, because the tokens had already been issued to a trusted app.

So I threat modeled the most common agent pattern in the enterprise, one box and one arrow at a time, using plain old STRIDE.

My take: over-broad scopes are the root problem, and logs that can't tell the agent from the human are a close second. The fixes are boring, which is good news.

Inside the article: the scenario, the threats that matter most, five controls, and four questions for your team.

Agentic Identity Notes, week 2. If you had to revoke one AI app's tokens across every tenant today, how long would it take?

#IdentitySecurity #AgenticAI #OAuth #CISO #SaaSSecurity

## Results (fill in after 7 days)

| Impressions | Reactions | Comments | Reposts | Article views | Profile views |
|---|---|---|---|---|---|
| | | | | | |

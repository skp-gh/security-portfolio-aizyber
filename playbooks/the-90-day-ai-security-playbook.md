# The 90-Day AI Security Playbook: How to Actually Execute the Three Moves

*A practical, week-by-week guide to closing the enterprise AI security gap. Built for CISOs and their teams, not for slideware.*

The last post argued that three moves matter in the next 90 days: get visibility, constrain blast radius, and write a usable policy. This is the execution guide. Real timelines, named tool categories, the pitfalls that derail each phase, and what "done" looks like at each checkpoint.

The plan assumes a mid-to-large enterprise with existing security infrastructure (DLP, SWG, SSO, EDR) and at least one approved GenAI tool already in flight. Adjust pace for your maturity, not the scope.

---

## Move 1: Get Visibility (Days 1 to 30)

You cannot govern AI usage you cannot see. The browser is where the leak happens, so that is where visibility has to start. Network DLP and email gateway logs will not show you the engineer pasting source code into a personal Claude account, the product manager uploading a customer list to a free Gemini tab, or the marketing intern feeding the brand voice doc into an unsanctioned writing assistant.

### Week 1: Stand up discovery

Pick one of three data sources to seed your AI tool inventory. Most teams already have at least one of them.

The first option is a SaaS Security Posture Management (SSPM) platform with shadow AI discovery. Reco, Obsidian Security, AppOmni, and Grip Security all surface AI-related SaaS connections through OAuth grants, finance data, and identity logs. Obsidian and Reco specifically market shadow AI discovery as a top-line capability in 2026. If you already license one of these, turn the AI discovery module on this week.

The second option is a secure web gateway or SASE platform. Zscaler, Netskope, Palo Alto Prisma Access, and Cisco Umbrella all have AI category lists and prompt-aware inspection now. They will tell you which AI domains your employees hit and how often, but they will not always see what gets pasted.

The third option is a browser-layer tool. This is where you get prompt-level visibility, and it is the gap most enterprises have. LayerX, Seraphic, and Menlo Security run as browser extensions and can monitor prompts, file uploads, and copy-paste into AI tabs. If you want to replace the browser entirely, Island and Palo Alto Prisma Access Browser (formerly Talon) give you a managed Chromium with native DLP. Replacing the browser is the heavier rollout but the strongest enforcement story.

Start with whichever data source you can light up fastest. Discovery in week one beats perfect discovery in month three.

### Week 2: Build the live inventory

Combine the discovery feeds into a single inventory table. For each AI tool found, capture: tool name, vendor, business owner (or "unknown"), number of distinct users, data sensitivity it has touched, whether it has an enterprise contract or is consumer-tier, and whether it has any plugins, agents, or MCP connectors enabled.

The MCP question matters. An AI tool with no connectors is a chatbot. An AI tool wired into Salesforce, Jira, Slack, or a code repo through MCP or a native integration is an agent with reach, and it belongs on a different risk tier.

### Week 3: Triage and label

Sort the inventory into three buckets. Sanctioned tools are the ones you have a contract for, a data processing agreement, and SSO enforcement on. Tolerated tools are personal-tier products you are not ready to block but want to monitor and warn on. Blocked tools are the ones with known data residency, training-on-input, or regulatory red flags.

DeepSeek, after the January 2026 exposure incident, is the easy example of "block now, revisit later." Anything that does not let you opt out of training is a candidate for the blocked list if your data classification policy says so.

### Week 4: Make discovery continuous

Set the discovery process to run weekly, not as a one-time audit. New AI tools ship every week. A vendor count that is stable month over month means your discovery is broken, not that your employees have stopped finding new tools.

**Done at day 30 looks like:** a named owner for the AI inventory, a refreshed list weekly, browser-level visibility on at least the top 80% of AI traffic, and a tiering decision on every tool with more than five users.

### Pitfalls to avoid

Do not start by writing a block list. You will be wrong about which tools matter and you will lose credibility with the business when you block something a team is already shipping with. Discover first, label second, block last.

Do not let the AI inventory live in a spreadsheet maintained by one person. Park it in your CMDB, ServiceNow, or a SaaS management platform where the data refreshes on its own.

---

## Move 2: Constrain Blast Radius (Days 31 to 60)

Visibility tells you what is happening. Constraint decides what each tool is allowed to do when it acts on your data. The threat model has shifted from "the model says something bad" to "the agent does something bad." The ServiceNow Now Assist incident, where a low-privilege agent tricked a higher-privilege agent into exporting an entire case file, is the textbook case. The fix is least agency, applied to every AI integration in the environment.

### Week 5: Inventory agent permissions

For every sanctioned AI tool that touches systems (Copilot for Microsoft 365, Salesforce Einstein, Glean, custom agents built on AWS Bedrock or Azure AI Foundry, MCP servers in developer IDEs), pull the actual permission grants. Not the documented permissions. The real ones.

You are looking for: which user identities the agent runs as, which roles or scopes it inherits, which tools or APIs it can call, which data stores it can read or write, and whether it can trigger another agent.

### Week 6: Apply least agency

For each agent, ask three questions. What is the minimum functionality this agent needs to do its job? What is the minimum permission level for that functionality? What is the minimum autonomy: which actions can it take without a human in the loop?

Cut everything that fails the minimum test. Most enterprise AI integrations were configured during a proof of concept and never pruned. Expect to find agents with read access to data stores they have not queried in months and write access to systems no one remembers granting.

### Week 7: Add approval gates for high-impact actions

Define the list of actions that always require a human approval step. The defensible default is: any export, payment, transfer, ticket closure, code merge, calendar send, customer-facing email, or schema change. Wire those actions to require an interactive approval (not a notification, not an audit log, an actual click) before the agent can complete them.

If your AI platform does not support approval gating natively, route the action through a workflow tool (ServiceNow, Jira, Zapier, or a custom approval service) that does.

### Week 8: Deploy runtime guardrails

For any agent that processes external input (customer chat, email content, web pages, RAG documents from outside sources), add an input/output guardrail layer. The options break into three categories.

Managed runtime guardrails include Lakera Guard (now part of Check Point), Prompt Security (now part of SentinelOne), and Protect AI Guardian (now part of Palo Alto). These are API-tier services with sub-100ms latency and high detection rates on prompt injection, jailbreaks, and data leakage.

Self-hosted options include Protect AI's open-source LLM Guard, NVIDIA NeMo Guardrails, and OpenAI's Guardrails framework. Use these when data residency or air-gap requirements rule out a managed service.

Platform-native options include Azure AI Content Safety, AWS Bedrock Guardrails, and Google Vertex AI safety filters. These come bundled when you build on the underlying platform and are the lowest-friction starting point if you are not yet ready to pick a dedicated vendor.

Treat agent-to-agent calls the same as service account calls. Authenticate them, scope their permissions, log them, and rate-limit them.

**Done at day 60 looks like:** every sanctioned agent has a documented permission scope, every high-impact action route has an approval gate, every externally-facing agent has a guardrail layer, and agent-to-agent calls are inventoried.

---

## Move 3: Write a Usable Policy (Days 61 to 90)

The biggest policy mistake is length. A 40-page AI governance document looks thorough and changes no behavior. The goal of the policy is to be the thing an engineer reads when they are 30 seconds away from pasting a customer list into a new AI tool. If it takes more than 30 seconds to get the answer, the policy lost.

### Week 9: Draft a two-page acceptable use standard

Cover six things and nothing else. Which AI tools are approved (named, with a link to access). Which data classes are off-limits in any AI tool (named, with examples). Which data classes are allowed only in approved tools. How to request a new tool (a single form, a single owner, a published SLA). What happens when an unsanctioned tool is detected (warning, then block, with a path to sanction it). Who to contact with a question (a Slack channel, not a mailbox nobody watches).

A drop-in template is included in the companion file `ai-acceptable-use-policy-template.md`. It is two pages. It is meant to be edited, not adopted verbatim.

### Week 10: Stand up sanctioned alternatives

For every category an employee might reach for a personal AI tool, give them a sanctioned one. General-purpose chat assistant. Code assistant. Meeting summarizer. Document drafting. Image generation. If the answer to "can I use an AI tool for X?" is "yes, use this one," shadow AI loses most of its pull. If the only sanctioned answer is no, shadow AI wins.

Microsoft 365 Copilot, ChatGPT Enterprise, Claude for Enterprise, Glean, GitHub Copilot Enterprise, Gemini for Workspace, and Cursor Business tier are the most common enterprise-grade options. Pick one per category and make access easy.

### Week 11: Communicate, do not announce

Roll the policy through the channels people actually use. A Slack post and a 15-minute brown bag will outperform a company-wide email. Tell three stories: a real internal example of shadow AI saving someone time (a positive frame), a real internal or industry example of where it went wrong (the consequence frame), and the one-page summary of what to do (the action frame).

### Week 12: Make compliance visible

Add an AI usage dashboard to your monthly security review. Track: percentage of AI traffic going to sanctioned tools, count of new tools detected, count of policy violations and how they were resolved, and time from new-tool request to decision. The metrics tell the executive team the program is working, and they tell the workforce that someone is paying attention.

**Done at day 90 looks like:** a two-page policy published and findable, a sanctioned alternative in every category, an active comms cadence, and a dashboard your CIO can read in 60 seconds.

---

## The 90-day cadence at a glance

Days 1 to 30: discover. Stand up browser and SaaS visibility, build the inventory, label every tool, set discovery to weekly.

Days 31 to 60: constrain. Inventory agent permissions, apply least agency, add approval gates for high-impact actions, deploy runtime guardrails.

Days 61 to 90: codify. Publish a two-page policy, stand up sanctioned alternatives, communicate through real channels, make compliance visible.

## What you will not finish in 90 days

You will not finish the agentic AI risk story. You will not finish the third-party AI vendor review program. You will not finish red-teaming every model. Those are quarter two and three problems, and the playbook above is what earns you the right to work on them.

The point of 90 days is to get the gap closed enough that the next incident, when it happens, is one you can see, contain, and explain. That is the bar.

---

## Sources

- [LayerX: AI Governance, AI Usage Control & Browser Security](https://layerxsecurity.com/)
- [Island vs Talon vs LayerX Enterprise Browser Buyer Guide, Kahana](https://kahana.co/blog/island-talon-layerx-enterprise-browser-buyer-guide-2026)
- [Securely Use Generative AI with Zscaler Zero Trust Exchange](https://www.zscaler.com/products-and-solutions/securing-generative-ai)
- [Top SaaS Security Tools to Protect Your Organization in 2026, Reco](https://www.reco.ai/compare/saas-security-tools)
- [Obsidian Security vs AppOmni: Key Differences, Reco](https://www.reco.ai/compare/obsidian-security-vs-appomni)
- [Lakera Guard: AI Agent Security Runtime Visibility and Protection](https://www.lakera.ai/lakera-guard)
- [Best AI Agent Guardrails Solutions in 2026, Galileo](https://galileo.ai/blog/best-ai-agent-guardrails-solutions)
- [The Complete AI Guardrails Implementation Guide for 2026, Maxim](https://www.getmaxim.ai/articles/the-complete-ai-guardrails-implementation-guide-for-2026/)
- [Top AI Security Tools for the Cloud, Wiz](https://www.wiz.io/academy/ai-security/ai-security-tools)
- [OWASP Top 10 for LLM Applications (2025)](https://genai.owasp.org/llm-top-10/)
- [CISO AI Risk Report 2026, Saviynt](https://saviynt.com/ciso-ai-risk-report-2026)
- [The AI Agent Governance Gap, Cloud Security Alliance](https://labs.cloudsecurityalliance.org/research/csa-research-note-ai-agent-governance-framework-gap-20260403/)

# AI Security: The Gap CISOs Can't Keep Ignoring

*An expert take on where enterprise GenAI risk actually lives, and what to do about it this quarter.*

Most CISOs I talk to already know their employees are using ChatGPT, Copilot, Gemini, and a dozen tools nobody on the security team has heard of. What they often underestimate is how far past the policy line the organization has already moved. Three out of four CISOs report discovering unsanctioned GenAI tools running in production, and 98% of organizations have employees using AI tools that were never approved. Only 37% have an AI governance policy on paper, and far fewer have one that actually works in the browser.

That is the gap. Not a future risk. A present one.

## What's actually happening

The threat model for LLMs is no longer theoretical. In January 2026, Varonis disclosed "Reprompt," a single-click data exfiltration path in Microsoft Copilot Personal. A user clicking a legitimate-looking Microsoft link was enough to trigger compromise. ServiceNow's Now Assist was hit by a second-order prompt injection where a low-privilege agent tricked a higher-privilege agent into exporting an entire case file to an attacker-controlled URL. DeepSeek shipped to global enterprise users with exposed databases. GreyNoise tracked more than 91,000 attack sessions targeting exposed LLM services in a single quarter.

The pattern is consistent. Attackers are not breaking the model. They are abusing the trust boundary between the model, its tools, and the data it can reach. OWASP's 2025 Top 10 for LLM Applications puts prompt injection at number one and reports it appearing in 73% of production AI deployments audited.

Meanwhile, the data flowing the other way is just as bad. A 2025 LayerX study found 77% of employees who use AI at work have pasted company data into a chatbot, and 22% of those pastes contained confidential personal or financial information. That is not a phishing campaign or a zero-day. That is the workday.

## Why the existing controls don't catch it

Traditional DLP was built for email, USB, and network egress. None of those channels are where the leak now happens. A user copies a paragraph from a contract, pastes it into a browser-based AI tool, and there is no attachment, no file transfer, and no network event a perimeter-focused DLP can hang a rule on. SaaS-level visibility, browser telemetry, and prompt-aware inspection are what's needed, and most organizations don't have any of the three.

The governance gap is wider. Compliance Week's 2026 survey found 83% of organizations are using AI, but only 25% have implemented a meaningful governance framework. NIST's AI RMF and ISO 42001 exist, but adoption is uneven, and neither was originally designed for the agentic systems now rolling into production. Gartner projects 40% of enterprise applications will embed task-specific AI agents by the end of 2026, most of them with privileges no one has audited.

## Why this should be on the board agenda

IBM's data puts the average breach cost for organizations with high shadow AI at $4.63 million, about $670,000 above peers. Annual insider risk costs reached $19.5 million per organization, with more than half driven by non-malicious actors, mostly shadow AI negligence. The dollar figures translate the technical risk into something a board will act on: this is a cost line, not a theoretical concern.

There is also a reputational dimension. The Cisco/Splunk CISO Report found 86% of CISOs expect agentic AI to expand the social engineering attack surface. Deepfake-driven fraud, prompt-injected agents acting on behalf of executives, and data leaks via SaaS AI copilots are now plausible front-page incidents.

## A mitigation plan that fits the quarter

Forget the multi-year roadmap. Three moves matter in the next 90 days.

First, get visibility. Deploy browser and SaaS telemetry that can see prompts and uploads, not just network flows. You cannot govern what you cannot see, and the leak channel today is the browser, not the firewall. Build a live inventory of every AI tool, plugin, agent, and MCP connector touching your data, including the ones IT didn't sanction.

Second, constrain the blast radius. Apply the principle of least agency to every AI integration. Limit what tools an agent can call, scope its credentials, and require a human approval step for any high-impact action: exports, payments, ticket closures, code merges. The ServiceNow incident is the textbook case for why peer-trust between agents needs the same scrutiny you would give a service account.

Third, write a usable policy, not a posture. A two-page AI acceptable-use standard that names which tools are approved, what data classes are off-limits, and how to request a new tool will move more behavior than a 40-page governance binder no one reads. Pair it with a sanctioned, enterprise-tier alternative. If the only approved path is "don't use AI," shadow AI wins.

## The honest takeaway

The technology is moving faster than the controls, and the controls most teams already own were not built for this surface. The CISOs who close the gap this year will not be the ones with the most elegant framework. They will be the ones who get visibility into the browser, put guardrails on agency, and give their workforce a sanctioned way to do the thing they are already doing.

The risk is not that AI is dangerous. The risk is that it is already in production, and the security program is not.

---

## Sources

- [OWASP Top 10 for LLM Applications (2025)](https://genai.owasp.org/llm-top-10/)
- [OWASP LLM Top 10 2025: Key Risks and Mitigation Strategies, Invicti](https://www.invicti.com/blog/web-security/owasp-top-10-risks-llm-security-2025)
- [Prompt Injection Attacks: The Most Common AI Exploit in 2025, Obsidian Security](https://www.obsidiansecurity.com/blog/prompt-injection)
- [LLM Security Risks in 2026: Prompt Injection, RAG, and Shadow AI, Sombra](https://sombrainc.com/blog/llm-security-risks-2026)
- [2026 CISO AI Risk Report, Cybersecurity Insiders](https://www.cybersecurity-insiders.com/2026-ciso-ai-risk-report/)
- [CISO AI Risk Report 2026, Saviynt](https://saviynt.com/ciso-ai-risk-report-2026)
- [Shadow AI risks deepen as 31% of users get no employer training, Help Net Security](https://www.helpnetsecurity.com/2026/05/01/shadow-ai-risks-it-oversight/)
- [Shadow AI Risks: Why 54% of Employees Use Unsanctioned AI, Iternal](https://iternal.ai/shadow-ai-risks)
- [AI Risk Management 2026: Shadow AI, Agentic Risks & NIST, UnderDefense](https://underdefense.com/blog/ai-risk-management/)
- [The AI Agent Governance Gap: What CISOs Need Now, Cloud Security Alliance](https://labs.cloudsecurityalliance.org/research/csa-research-note-ai-agent-governance-framework-gap-20260403/)
- [AI Data Loss Prevention: Why Traditional DLP Fails LLMs, BeyondScale](https://beyondscale.tech/blog/ai-data-loss-prevention-llm-enterprise)
- [Best Enterprise DLP Tools for AI Data Risk in 2026, Cyberhaven](https://www.cyberhaven.com/blog/best-enterprise-dlp-tools-ai-data-risk)
- [Prompt injection tags along as GenAI enters daily government use, Help Net Security](https://www.helpnetsecurity.com/2026/04/09/genai-prompt-injection-enterprise-data-risk/)

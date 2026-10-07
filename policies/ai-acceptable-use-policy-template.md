# AI Acceptable Use Standard

*Drop-in template. Two pages. Edit, do not adopt verbatim. Replace bracketed placeholders before publishing.*

**Owner:** [CISO or designated security leader]
**Effective date:** [date]
**Review cadence:** Quarterly
**Audience:** All employees, contractors, and vendors with access to [Company] systems or data

---

## 1. Purpose

This standard tells you which AI tools you can use, what data you can put into them, and how to get a new tool approved. It exists because AI tools can leak sensitive data, generate inaccurate output, or take unintended actions on your behalf. Following this standard protects you, your team, and the company.

---

## 2. Approved AI Tools

You may use these tools without additional approval. Access is provisioned through SSO. Personal accounts on these services are not covered by this standard and are treated as unsanctioned.

| Use case | Approved tool | How to access |
|---|---|---|
| General-purpose AI chat and drafting | [ChatGPT Enterprise / Claude for Enterprise / Gemini for Workspace] | [SSO link] |
| Code generation and review | [GitHub Copilot Enterprise / Cursor Business] | [SSO link] |
| Meeting summarization and notes | [Microsoft Copilot for M365 / Otter Business / Read AI] | Available in [Teams / Zoom] |
| Enterprise search and knowledge | [Glean / Microsoft Copilot for M365] | [SSO link] |
| Image and design generation | [Adobe Firefly Enterprise / Microsoft Designer] | [SSO link] |
| Customer-facing AI features | Only models reviewed by Security and Legal. Email `ai-help` to start. | N/A |

The list is reviewed monthly. The current version is always at [internal wiki link].

---

## 3. Data Classes and Where They Can Go

[Company] data is classified into four tiers. AI usage by tier is as follows.

**Restricted data.** Customer PII, payment data, health records, source code marked sensitive, M&A material, unannounced financial results, security vulnerabilities, employee compensation, and anything covered by [GDPR / HIPAA / PCI / SOX / regional regulation]. May only be used in approved tools marked "Restricted-OK" in the table above. Never in personal-tier or unsanctioned tools.

**Internal data.** Non-public business documents, internal communications, project plans, and most source code. May be used in any tool in the approved list. Never in personal-tier or unsanctioned tools.

**Confidential third-party data.** Anything covered by an NDA or DPA with a customer, partner, or vendor. Treat as Restricted unless the contract explicitly permits AI processing.

**Public data.** Marketing copy, published documentation, public website content. May be used in any tool, including personal-tier, with normal judgment.

When in doubt, treat it as Internal.

---

## 4. What You Must Not Do

Do not paste, upload, or type Restricted or Internal data into any AI tool not on the approved list. This includes free-tier ChatGPT, free-tier Claude, free-tier Gemini, DeepSeek, Perplexity (consumer tier), and any AI tool with a personal email login.

Do not give an AI agent or assistant standing access to production systems, customer data stores, or financial systems without a Security review.

Do not let an AI tool take a high-impact action (exports, payments, transfers, external sends, code merges to production, contract signing, customer-facing communication) without a human approving each action.

Do not bypass approval gates, security warnings, or content filters in approved AI tools.

Do not use AI to generate content that impersonates a real person, including coworkers, executives, or customers, without their explicit consent.

Do not rely on AI output for decisions about hiring, performance, legal positions, medical guidance, or anything regulated, without human review and the appropriate professional sign-off.

---

## 5. How to Request a New AI Tool

If you need a tool that is not on the approved list, submit the AI Tool Request form at [internal link]. The form asks for: the tool, the business use case, the data classes you need to process, who else on your team needs it, and whether a free version is acceptable.

Service level: initial response within two business days. Decision within ten business days for standard tools, longer for tools that require Legal or Privacy review.

Pre-approval is required before any pilot, proof of concept, or paid subscription, including team-tier and personal-tier purchases on a corporate card.

---

## 6. Detection and Enforcement

[Company] monitors AI tool usage through browser telemetry, SaaS posture management, and network gateway logs. Usage of unapproved tools triggers an in-tool warning and a notification to the user and their manager.

Repeated violations follow the standard progressive discipline path: coaching for the first instance, a formal warning for the second, escalation to HR for the third. Intentional exfiltration of Restricted data through any channel, AI or otherwise, may result in immediate termination and legal action.

Self-reporting an accidental violation within 24 hours is treated as a coaching event, not a disciplinary one. Use the `#ai-help` Slack channel.

---

## 7. Special Cases

**Customer-facing AI features.** Any AI capability that is exposed to customers, including chatbots, generated email, recommendation systems, and agentic workflows, requires Security and Legal review before launch. Use the AI Feature Review path at [internal link].

**AI in code repositories.** AI-generated code is allowed in approved code assistants. Code containing customer-identifying information, security secrets, or proprietary algorithms must not be used as training input even in approved tools where opt-out is available.

**AI agents and automations.** Any AI agent that can take actions in [Company] systems must be inventoried in the AI Agent Registry, have a documented owner, and operate under a service identity with scoped permissions. Standing access to production data or write access to financial systems requires a Security review.

**Third-party AI processing of [Company] data.** Any vendor that processes [Company] data with AI, whether disclosed or not, must be covered by a current DPA that addresses AI processing, training opt-out, and data retention.

---

## 8. Roles and Responsibilities

The CISO owns this standard and the approved tool list.

The AI Governance Council ([CISO, CIO, CDO, General Counsel, Privacy Officer]) reviews changes monthly.

Department heads are responsible for their team's compliance and for surfacing new AI tool needs.

Every employee is responsible for following this standard and asking when unsure.

---

## 9. Related Documents

[Data Classification Policy] | [Information Security Policy] | [Third-Party Risk Standard] | [Code of Conduct] | [Privacy Policy] | [AI Feature Review Process]

---

## 10. Version History

| Version | Date | Author | Change |
|---|---|---|---|
| 1.0 | [date] | [CISO name] | Initial publication |

---

*This standard is short by design.*

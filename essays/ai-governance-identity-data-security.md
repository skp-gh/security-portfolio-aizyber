# The Identity and Data Security Convergence: Governing AI at Enterprise Scale

You can't govern AI at enterprise scale without identity and data security moving in lockstep. This isn't a theoretical construct or vendor marketing speak. It's becoming operational reality.

Last month, Okta announced an integration between their Identity Security Posture Management (ISPM) platform and Anthropic's Claude Compliance API. The same week, Proofpoint unveiled their own integration, extending data loss prevention, insider threat detection, and Digital Communications Governance into Claude workflows. These aren't isolated announcements. They're signals of a fundamental shift in how we think about enterprise security in an AI-enabled world.

And they're forcing us to ask hard questions: If a compromised identity can unlock your entire AI ecosystem, do you actually have visibility into who's accessing Claude? When AI assistants handle sensitive data alongside human employees, are your DLP policies even capable of protecting it? Can you investigate an AI-assisted decision that later becomes relevant to regulators or lawyers without understanding the identity, the data, and the communications involved?

The answer to all three is no. Not yet. And that's the problem we need to solve.

## The Assumption That Broke

For two decades, enterprise security has operated under a foundational assumption: humans are the actors in the system.

A human sends the email. A human downloads the file. A human accesses the database. A human makes the decision that a regulator later asks about. Build your controls around that assumption and you get DLP that triggers on human behavior, insider risk models that track human patterns, identity systems that govern human access, and investigation workflows that reconstruct what humans did.

Then AI arrived.

Now, AI assistants and agents don't just support human decisions. They help employees read sensitive files, consolidate regulated data, draft communications, summarize discussions, and trigger workflows across critical systems. In many cases, they're operating in the exact same environments where sensitive data, regulated communications, and business decisions already live.

The question isn't whether AI has access to sensitive information. It does. The question that should keep you awake at night is whether you can see that access, govern it, and investigate it if something goes wrong.

Most enterprise security programs can't. And that's because we've been operating with separated control architectures. Data security here. Identity governance there. AI policies somewhere else entirely.

## The Identity Problem You Haven't Solved

Let's start with identity, because it's the foundation everything else sits on.

Think about your Claude deployment. Who can access it? Who has admin keys? When was the last time you rotated those keys? Do you know which admin API keys are actually being used versus dormant ones that represent security debt? Can you instantly verify that an offboarded employee no longer has access to your corporate Claude workspace?

If you hesitated on any of those questions, Okta's integration with the Claude Compliance API is pointing at exactly the gap you're trying to close.

Identity has always been the control plane that matters most. One compromised identity in your email system can unlock entire inboxes. One compromised API key in your cloud environment can drain your data. But here's what makes AI different: one compromised identity in Claude can give an attacker access not just to data, but to a system that can synthesize that data, combine it with information from connected tools, and act on it with apparent authority.

A single admin key that's been rotated quarterly for email is still a significant risk. A single admin key for Claude that hasn't been rotated in a year? That's a breach waiting to happen. And most organizations don't even know which category they fall into.

Okta's integration gives you visibility into exactly that. Through their Identity Security Posture Management platform, you can now see which admin API keys exist in Claude, which ones are in use, which ones haven't been rotated, and which ones belong to people who no longer work here. You can identify privilege escalation risks. You can flag dormant accounts. You can verify that your offboarding process actually revokes Claude access before someone else is using the workspace.

That's not AI governance. That's identity governance applied to AI. And it's the prerequisite for everything else.

## The Data Problem You Can't Ignore

But identity alone isn't enough. Because having access to Claude and actually extracting sensitive data from Claude are two different problems.

This is where Proofpoint's integration becomes critical.

Proofpoint spent two decades building data loss prevention and insider risk detection around a simple observation: the context around data movement matters as much as the data itself. It's not just about detecting when a file moves. It's about understanding who moved it, why they might have moved it, what other data they accessed around the same time, and whether the pattern suggests malicious intent or carelessness.

Those behavioral models and classification systems? They were built for human actors. When an employee downloads 500 files before their resignation date, that's a pattern Proofpoint's algorithms have learned to recognize as a specific kind of risk. When that same employee starts accessing confidential data they've never touched before, right before a competitive hiring announcement, that's another pattern.

Now imagine those same patterns in Claude.

Claude Enterprise can help an employee read those files. Understand them. Consolidate information from multiple sources. Draft communications about it. The AI isn't doing anything wrong. It's just helping the employee work. But if the employee is exfiltrating sensitive data, the AI becomes part of the exfiltration workflow. And if your security controls can't see Claude activity, you can't see half the exfiltration.

Proofpoint's integration brings Claude Enterprise conversation content and Claude Platform activity logs into their existing data security, insider risk, and Digital Communications Governance workflows. Your existing DLP classifiers? They now apply to Claude. Your behavioral risk models? They now analyze Claude activity. Your investigation workflows? They can now reconstruct what happened across human actions and AI-assisted steps.

This matters because it means you're not standing up a separate AI DLP system. You're extending the controls you already operate to cover AI. That's harder than it sounds, because AI introduces variables that traditional DLP wasn't built to handle. But it's harder to avoid than to do.

## The Convergence That Changes Everything

Here's the insight that ties these two announcements together:

Identity and data security have historically been separate disciplines with separate teams, separate budgets, and separate tooling. Your identity team asks: who can access what? Your data security team asks: what data is moving and how do we prevent loss? They don't always talk.

AI forces them to talk. And to think together.

Because a complete view of risk in Claude requires both dimensions. You need to know who's accessing Claude (identity). You need to know what data they can access through Claude (data security). You need to understand whether the combination of that identity plus that data represents actual risk (behavioral analysis). And you need to be able to investigate and reconstruct what happened if you're wrong about the risk assessment (communications governance and eDiscovery).

That's not four separate problems. It's one unified control problem with four dimensions.

Proofpoint's blog post on this integration frames it as extending "one platform view across data, behavior, communications, agents, and investigations." Okta frames it as applying identity governance across the full agent lifecycle. Both are saying the same thing: the control plane for AI in the enterprise is not a separate stack. It's an extension of the controls you already operate, with visibility and governance across both human and AI-driven activity.

The market instinct right now is to bolt on AI-specific tools. AI-native DLP. AI-native insider threat. AI-native governance. That creates tooling fragmentation and operational complexity. It means your security team has to maintain separate policies for AI versus email versus cloud. It means your incident response playbook for "employee accessed confidential data" doesn't apply when that employee asked Claude to summarize confidential data. It means you're building new investigation workflows instead of extending existing ones.

The better answer is unified governance. One set of classifiers that apply to human communications and AI-assisted communications. One set of identity controls that apply to human identities and AI agent identities. One investigation workflow that can reconstruct what happened whether the actor was a person or an AI system.

That's hard. It requires rethinking how your controls work when the data moving through them comes from an AI system rather than a human action. It requires identity and data security teams to collaborate instead of operate independently. It requires your CISO to have a coherent governance model that treats AI as part of the enterprise fabric rather than a new security domain.

But it's becoming unavoidable.

## The Questions You Need to Answer Now

As you evaluate Claude deployments or other enterprise AI platforms, ask your team these questions:

Can you see all the identities accessing your AI platform? Not just users, but service accounts, API keys, and any other form of non-human identity that connects to it?

Can you verify that your offboarding process actually revokes AI access? Or are there still-active identities in Claude belonging to people who left the company months ago?

Do you have visibility into what data is being processed through Claude? Not just inferred through logs, but actual observation of the files, prompts, and data that move through the system?

Can you apply your existing DLP policies to Claude activity? Or do you need a separate set of "AI DLP" rules that your team has to maintain in parallel?

Can you detect insider risk patterns in Claude-assisted workflows? Or does your insider threat program only see the human side of the action?

Can you investigate an incident that involves both human actions and AI-assisted steps? Or would you need to manually reconstruct what happened without visibility into what the AI saw or produced?

If you answered no to any of those questions, you're not alone. Most organizations can't answer yes. And that's why these integrations matter. They're not just features. They're acknowledgment that your existing control architecture needs to extend into AI, and that identity and data security need to move together to make that happen.

## The Conclusion That Matters

The vendors are getting ahead of the market, but the market is catching up fast.

Okta and Proofpoint aren't solving AI governance with these integrations. They're solving the prerequisite: extending identity and data security into AI platforms so that unified governance becomes possible. They're acknowledging that you can't govern Claude like you govern email, because Claude touches data and creates decisions and leaves records that exist across both human and AI actions.

So they're extending identity visibility into Claude. Extending DLP into Claude. Extending insider threat detection into Claude. Extending investigation workflows into Claude. Not as separate "AI" tools, but as extensions of tools you already operate.

That's the convergence that changes everything. Because once identity and data security are both visible in Claude, once they're both under control, once they're both part of a unified investigation workflow, you have a foundation for AI governance at enterprise scale.

Everything else builds on that foundation. The policies. The detection models. The investigation playbooks. The compliance mappings. The incident response workflows. All of it flows from having unified visibility into identity plus data plus behavior.

You can't govern AI at enterprise scale without that. Not because AI is magic. But because AI operates in the same environments where your most sensitive data lives, it connects to the same people who need to access that data, and it leaves records that may become evidence in a regulatory inquiry, a legal proceeding, or an internal investigation.

That's why identity and data security have to move in lockstep. And why these announcements from Okta and Proofpoint matter more than they might initially appear.

The question isn't whether you'll eventually need unified visibility into identity and data in your AI systems. You will. The only question is whether you'll have it when you need it.

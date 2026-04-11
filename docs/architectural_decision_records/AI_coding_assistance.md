# AI coding assistance decision record

## Date
2026-04-11

## Author
Steff Horemans 

## Context
AI assistants hold great promise to accelerate the completion of coding tasks, but they have also presented unique challenges.
- Actual productivity impact results on coding/highly skilled work are mixed:
    - This study report significantly higher productivity, specifically for people who are less experienced or under a high workload: https://arxiv.org/pdf/2302.06590
    - This study reported experienced developers being slower, but perceiving themselves as faster: https://arxiv.org/pdf/2507.09089
    - This report presents the most comprehensive study in the wild ans shows 10%-30% increase in developer effectiveness: https://economics.mit.edu/sites/default/files/inline-files/draft_copilot_experiments.pdf
    - Since the field is moving fast, it is difficult to assess how true these assessments remain at subsequent model releases. However, it seems that they do help with automating grunt work, which is in line with personal experience

- Reliance on AI coding tools seems to be causing cognitive atrophy and skill degradation:
    - This paper suggests that AI assistance can degrade the development of coding skills, especially if they were used for delegation rather than explanation. It contains AI interaction patterns that preserve learning: https://arxiv.org/html/2601.20245v1
    - This paper suggests that higher confidence in AI versus own skills leads to less critical thinking and being unprepared to handle exceptions when they do arise: https://www.microsoft.com/en-us/research/wp-content/uploads/2025/01/lee_2025_ai_critical_thinking_survey.pdf
    - This paper also suggests that AI use can cause skill erosion (especially with automation and offloading of reasoning) and that AI leads people to feel that tasks are easier, which causes them to overestimate their abilities: https://pmc.ncbi.nlm.nih.gov/articles/PMC11239631/
    - This paper discusses how to manage cognitive atrophy effectively, but requires a deeper read: https://www.mdpi.com/2078-2489/16/11/1009

-Burnout, Stress and Job Meaning:
    - This survey by Atlassian suggests that AI saves developers significant time, which they proceed to lose due to organizational inefficiencies and also mentions a growing empathy gap between developers and their managers: https://www.atlassian.com/blog/developer/developer-experience-report-2025  
    - This survey of developers  suggest frustrations with fixing "almost right AI code": https://survey.stackoverflow.co/2025/ai/
    - Employment of early-career tech workers has started to decline: https://digitaleconomy.stanford.edu/app/uploads/2025/11/CanariesintheCoalMine_Nov25.pdf https://spectrum.ieee.org/ai-effect-entry-level-jobs
    - This Github blog traces the evolution of a low-trust user of AI to a high level user of AI as a strategist. https://github.blog/news-insights/octoverse/the-new-identity-of-a-developer-what-changes-and-what-doesnt-in-the-ai-era/
    - Steve Yegge (writer of the Vibe Coding book on this topic) describes severe exhaustion caused by high agentic use: https://steve-yegge.medium.com/the-ai-vampire-eda6e4f07163

This is only a start of the challenges, which also include quality and security issues due to hallucinations.

## Decision
Given this early evidence, I have decided to start by playing it safe. I use the Anthropic AI fluency Framework to systematically frame my thinking on effective AI use: https://aifluencyframework.org/

### Delegation

### Description
### Discernment
### Diligence
We will keep a collection of records for "architecturally significant" decisions: those that affect the structure, non-functional characteristics, dependencies, interfaces, or construction techniques.

An architecture decision record is a short text file in a format similar to an Alexandrian pattern. (Though the decisions themselves are not necessarily patterns, they share the characteristic balancing of forces.) Each record describes a set of forces and a single decision in response to those forces. Note that the decision is the central piece here, so specific forces may appear in multiple ADRs.

- We will keep ADRs in the project repository under docs/architectural_decision_records/XXX.md

- We should use a lightweight text formatting language like Markdown or Textile.

- ADRs will be named in relation to their decision field (e.g. AI assistance). To avoid clutter and keep version history, decision changes will be documented as new versions of the markdown file. The original approach suggests keeping the file around, but that seems a bit excessive to me. Git versioning is sufficient for code versioning, so it should be sufficient for these decisions as well.



- We will use a format with just a few parts, so each document is easy to digest. The format has just a few parts.

Title These documents have names that are short noun phrases. For example, "ADR 1: Deployment on Ruby on Rails 3.0.10" or "ADR 9: LDAP for Multitenant Integration"

Context This section describes the forces at play, including technological, political, social, and project local. These forces are probably in tension, and should be called out as such. The language in this section is value-neutral. It is simply describing facts.

Decision This section describes our response to these forces. It is stated in full sentences, with active voice. "We will …"

Status A decision may be "proposed" if the project stakeholders haven't agreed with it yet, or "accepted" once it is agreed. 

Consequences This section describes the resulting context, after applying the decision. All consequences should be listed here, not just the "positive" ones. A particular decision may have positive, negative, and neutral consequences, but all of them affect the team and project in the future.

Version: Write down the version number if changes are made to an ADR. This should allow users to go back to past decisions

The whole document should be one or two pages long. We will write each ADR as if it is a conversation with a future developer. This requires good writing style, with full sentences organized into paragraphs. Bullets are acceptable only for visual style, not as an excuse for writing sentence fragments. (Bullets kill people, even PowerPoint bullets.)

## Status
Accepted.

## Consequences
One ADR describes one significant decision for a specific project. It should be something that has an effect on how the rest of the project will run.

The consequences of one ADR are very likely to become the context for subsequent ADRs. This is also similar to Alexander's idea of a pattern language: the large-scale responses create spaces for the smaller scale to fit into.

Developers and project stakeholders can see the ADRs, even as the team composition changes over time.

The motivation behind previous decisions is visible for everyone, present and future. Nobody is left scratching their heads to understand, "What were they thinking?" and the time to change old decisions will be clear from changes in the project's context.

## Version
1
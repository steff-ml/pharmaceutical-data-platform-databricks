# Scientific Decision Record documentation
This document is based on the famous post  on ADR by Michael Nygard. I will hold of on using tools for maintaining ADRs for now, since I don't see an added value for them at this point in time. 
URL: https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions
ADR tools: https://github.com/npryce/adr-tools?tab=readme-ov-file

## Date
2026-04-11

## Author
Steff Horemans 

## Context
https://pmc.ncbi.nlm.nih.gov/articles/PMC11239631/
https://www.mdpi.com/2075-4698/15/1/6
https://pmc.ncbi.nlm.nih.gov/articles/PMC11020077/
https://pmc.ncbi.nlm.nih.gov/articles/PMC12036037/
https://www.frontiersin.org/journals/education/articles/10.3389/feduc.2025.1711718/full
https://pmc.ncbi.nlm.nih.gov/articles/PMC12515416/
https://pmc.ncbi.nlm.nih.gov/articles/PMC12170296/
https://retractionwatch.com/2025/02/10/as-springer-nature-journal-clears-ai-papers-one-universitys-retractions-rise-drastically/
https://ukrio.org/wp-content/uploads/Embracing-AI-with-integrity.pdf
https://papers.ssrn.com/sol3/papers.cfm?abstract_id=4375283
https://pmc.ncbi.nlm.nih.gov/articles/PMC12245043/
https://pmc.ncbi.nlm.nih.gov/articles/PMC12569468/
https://arxiv.org/abs/2510.07435
Standout finding for your ADR context: Hao et al. is the most striking — AI makes individual researchers dramatically more productive by conventional metrics (papers, citations), but simultaneously contracts the collective diversity of science. That's a direct analog to the software finding that AI expands throughput metrics while degrading code quality and developer dept
Architectural Decision Records are used in Agile software development to solve several problems:
- Not all decisions will be made at once, nor will all of them be done when the project begins.
- Nobody ever reads large documents, either.
Architecture for agile projects has to be described and defined differently. Not all decisions will be made at once, nor will all of them be done when the project begins.
- One of the hardest things to track during the life of a project is the motivation behind certain decisions. Without proper rationale, a person has only the option to blindly accept or challenge a decision.

Scientific assumptions have a lot in common with Agile Architecture decisions:
- Scientific assumptions are subject to change, since the underlying science is constantly changing.
- Scientific decisions come from a sometimes opinionated synthesis of multiple research papers that might conflict. It feels appropriate to keep track of your thinking on certain topics.
- Scientific domain knowledge will heavily impact the architectural and logical decisions we make. They deserve to be tracked separately.

## Decision
We will keep a collection of records for "scientific significant" decisions: those that affect the structure, non-functional characteristics, dependencies, interfaces, or construction techniques or logic.

An scientific decision record is a short text file in a format similar to an Alexandrian pattern. (Though the decisions themselves are not necessarily patterns, they share the characteristic balancing of forces.) Each record describes a set of forces and a single decision in response to those forces. Note that the decision is the central piece here, so specific forces may appear in multiple ADRs.

- We will keep SDRs in the project repository under docs/scientific_decision_records/XXX.md

- We should use a lightweight text formatting language like Markdown or Textile.

- SDRs will be named in relation to their decision field (e.g. genetic_evidence_assumptions). To avoid clutter and keep version history, decision changes will be documented as new versions of the markdown file. The original approach suggests keeping the file around, but that seems a bit excessive to me. Git versioning is sufficient for code versioning, so it should be sufficient for these decisions as well.



- We will use a format with just a few parts, so each document is easy to digest. The format has just a few parts.

Title These documents have names that are short noun phrases. For example, "ADR 1: Deployment on Ruby on Rails 3.0.10" or "ADR 9: LDAP for Multitenant Integration"

Context This section describes the forces at play, including technological, political, social, and project local. These forces are probably in tension, and should be called out as such. The language in this section is value-neutral. It is simply describing facts. It also describes literature overviews and contested viewpoints of scientific merit

Decision This section describes our response to these forces. It is stated in full sentences, with active voice. "We will …"

Status A decision may be "proposed" if the project stakeholders haven't agreed with it yet, or "accepted" once it is agreed. 

Consequences This section describes the resulting context, after applying the decision. All consequences should be listed here, not just the "positive" ones. A particular decision may have positive, negative, and neutral consequences, but all of them affect the team and project in the future.

Version: Write down the version number if changes are made to an ADR. This should allow users to go back to past decisions

The whole document should be one or two pages long. We will write each ADR as if it is a conversation with a future developer. This requires good writing style, with full sentences organized into paragraphs. Bullets are acceptable only for visual style, not as an excuse for writing sentence fragments. (Bullets kill people, even PowerPoint bullets.)

## Status
Accepted.

## Consequences
One SDR describes one significant decision for a specific project. It should be something that has an effect on how the rest of the project will run.

The consequences of one ADR are very likely to become the context for subsequent ADRs. This is also similar to Alexander's idea of a pattern language: the large-scale responses create spaces for the smaller scale to fit into.

Developers and project stakeholders can see the ADRs, even as the team composition changes over time.

The motivation behind previous decisions is visible for everyone, present and future. Nobody is left scratching their heads to understand, "What were they thinking?" and the time to change old decisions will be clear from changes in the project's context.

## Version
1


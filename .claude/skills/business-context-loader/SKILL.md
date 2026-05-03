---
name: business-context-loader
description: |
  Loads and interprets the project business case document for use in
  architecture and business logic reviews. Load this skill when a review
  requires understanding of business rules, pipeline intent, data contracts,
  or protected logic. Always read docs/business-case.md before producing
  any architecture or business logic findings.
---

## Loading the Business Context

Before producing any findings that touch on business logic or architectural
intent, read `docs/business-case.md` using the Read tool.

If the file does not exist, include this note at the top of the report:
```
> ⚠️ docs/business-case.md not found. Business context unavailable.
> Architecture findings are based on code structure only.
> Create this file to enable business-aware reviews.
```

Do not invent business rules. If the document exists but does not cover a
topic relevant to the diff, say so explicitly rather than guessing.

---

## Interpreting the Business Case Document

When reading `docs/business-case.md`, extract and hold in context:

**Pipeline inventory** — what each pipeline is named, what it reads, what it
writes, and what it is for. Use this to flag when a change touches a pipeline
in an unexpected way.

**Business rules** — explicit statements about how data must be transformed,
filtered, or calculated. These are the primary check targets for the business
logic reviewer. Treat them as requirements — any code that contradicts them
is an ERROR.

**Protected logic** — sections marked as requiring sign-off before change.
Any diff that modifies protected logic must be flagged as CRITICAL regardless
of whether the change looks technically correct.

**Data source contracts** — known behaviours of upstream sources (e.g. "may
contain duplicates", "schema changes without notice"). Use these to evaluate
whether the code handles source quirks correctly.

**Out of scope statements** — things the pipelines explicitly do not do.
Do not flag the absence of these as issues.

---

## Cross-Referencing Diff Against Business Rules

For each business rule in the document, ask:
1. Does the diff touch code that implements this rule?
2. If yes — does the change preserve, extend, or contradict the rule?
3. If it contradicts — is there a comment or PR description explaining why?

Flag contradictions as **ERROR** with a direct quote of the business rule
being violated and the specific lines in the diff that contradict it.

Flag changes to protected logic as **CRITICAL** even if the change looks
correct — these require human sign-off by definition.

---

## When Business Context Is Ambiguous

If a diff changes logic that *might* relate to a business rule but it is
unclear, report it as **INFO** with the question to ask:

```
**INFO** `transformations/revenue.py:45` — this change modifies revenue
calculation logic. Business rule BR-04 defines revenue as excluding VAT.
Confirm this change is consistent with that definition.
```

Do not escalate ambiguity to ERROR or CRITICAL — only clear contradictions
warrant those severities.

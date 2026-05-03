# Business Case — {Project Name}

> This document is the reference source for the `de-biz-reviewer` agent.
> Keep it up to date as pipelines evolve. The agent checks code diffs against
> the rules and definitions here — vague or missing entries produce weaker reviews.

---

## 1. Pipeline Inventory

> One entry per pipeline. Include name, source, target, purpose, and owner.
> The agent uses these to identify which pipelines a diff affects.

### Pipeline: {pipeline_name}
- **Reads from**: `{schema.table}`, `{schema.table}`
- **Writes to**: `{schema.table}`
- **Refresh cadence**: {e.g. daily at 06:00 UTC / triggered on arrival}
- **Purpose**: {one paragraph describing what this pipeline does and why it exists}
- **Owner**: {team or person responsible}
- **Notebook/module path**: `{path/to/notebook_or_module.py}`

<!-- Add more pipelines by copying the block above -->

---

## 2. Business Rules

> Explicit, precise statements about how data must be transformed, filtered,
> or calculated. These are the primary check targets.
> Use unique IDs (BR-01, BR-02...) so findings can reference them clearly.
> Be specific — "revenue excludes VAT" is checkable; "handle revenue correctly" is not.

| ID | Rule | Pipeline(s) |
|----|------|-------------|
| BR-01 | {e.g. A customer is churned if no activity for 90 days} | {pipeline_name} |
| BR-02 | {e.g. Revenue figures exclude VAT and test accounts (account_type = 'test')} | {pipeline_name} |
| BR-03 | {e.g. The reporting week runs Monday–Sunday, not ISO calendar week} | {pipeline_name} |
| BR-04 | {e.g. Null values in customer_tier default to 'standard' — never dropped} | {pipeline_name} |

<!-- Add rows as needed -->

---

## 3. Protected Logic

> Logic that must not change without explicit sign-off. The agent flags any
> diff touching these sections as CRITICAL.

| ID | What is protected | Why | Sign-off required from |
|----|------------------|-----|------------------------|
| PL-01 | {e.g. The 90-day churn window definition} | {e.g. Changing it would break model training assumptions} | {e.g. Data Science lead} |
| PL-02 | {e.g. Deduplication key on silver.crm_events: (event_id, customer_id)} | {e.g. Changing this breaks all downstream models} | {e.g. Platform team} |

<!-- Add rows as needed -->

---

## 4. Data Source Contracts

> Known behaviours and quirks of upstream sources. The agent checks whether
> code reading from these sources handles the documented quirks.

### Source: `{schema.table}` / `{topic or API name}`
- **Type**: {Delta table / Kafka topic / REST API / CSV landing zone}
- **Refresh frequency**: {e.g. every 4 hours / real-time / daily batch}
- **Known quirks**:
  - {e.g. May contain duplicate records within a 10-minute arrival window}
  - {e.g. Schema changes without notice — always validate on read}
  - {e.g. Null values in customer_id are valid and represent anonymous users}
- **Deduplication key**: `{column(s)}` (if applicable)
- **Schema contract**: {strict / soft / none — describe what is guaranteed}

<!-- Add more sources by copying the block above -->

---

## 5. Explicitly Out of Scope

> Things these pipelines intentionally do NOT do.
> The agent will not flag absence of these as issues.

- {e.g. GDPR deletion requests are handled by a separate deletion pipeline — these pipelines do not delete records}
- {e.g. Currency conversion is not performed here — all monetary values are in EUR at source}
- {e.g. Real-time alerting is out of scope — this is a batch system}
- {e.g. PII masking is applied upstream in the ingestion layer — not repeated here}

---

## 6. Glossary

> Define domain terms used in business rules so the agent interprets them correctly.
> Skip terms that are self-evident.

| Term | Definition |
|------|------------|
| {e.g. Active customer} | {e.g. A customer with at least one event in the last 90 days} |
| {e.g. Reporting week} | {e.g. Monday 00:00 UTC to Sunday 23:59 UTC} |
| {e.g. Test account} | {e.g. Any account where account_type = 'test' in silver.accounts} |

---

## Change Log

> Record significant changes to this document so the agent can detect when
> the document itself may be out of sync with the codebase.

| Date | Changed by | What changed |
|------|------------|--------------|
| {YYYY-MM-DD} | {name} | Initial version |

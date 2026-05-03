# Contributing Guide

This document defines how we work in this repository. The commit conventions
here are not optional — the changelog agent reads commit prefixes to
categorise entries, and the LinkedIn agents consume those categories.
Inconsistent commits produce a broken changelog.

---

## Commit Message Conventions

Every commit message must follow this format:

```
{prefix}: {short description in present tense}

{optional body — explain why, not what}
```

### Required Prefixes

| Prefix | When to use | Changelog entry? |
|---|---|---|
| `feat:` | New functionality — a new pipeline, transformation, or feature | ✅ Yes |
| `fix:` | Bug fix — correcting wrong behaviour | ✅ Yes |
| `refactor:` | Code restructuring without behaviour change | ✅ Yes |
| `perf:` | Performance improvement — query tuning, caching, partitioning | ✅ Yes |
| `infra:` | Tooling, hooks, agents, CI/CD, dev environment | ✅ Yes |
| `docs:` | Documentation updates — README, CLAUDE.md, business case | ✅ If significant |
| `test:` | Adding or updating tests | ✅ If new strategy |
| `chore:` | Maintenance — dependency bumps, formatting, minor cleanup | ❌ Skipped |
| `wip:` | Work in progress — incomplete, do not review | ❌ Skipped |

### Examples

```
feat: add customer churn feature pipeline

Reads from silver.crm_events, produces gold.churn_feature_store.
Implements 12 features defined in business-case.md BR-07.
```

```
infra: switch post-commit hook from pre-commit to post-commit

VS Code commits via --allow-empty-message which clears the git index
before the hook fires. post-commit reads HEAD~1 HEAD instead.
```

```
fix: handle null customer_tier in silver transform

Nulls were propagating to gold layer, violating BR-04.
Now defaults to 'standard' per business case definition.
```

```
chore: bump ruff to 0.4.1
```

```
wip: experimenting with z-ordering on crm_events
```

---

## Commit Size

- **Commit frequently** — small commits produce better reviews and
  changelog entries
- **One logical change per commit** — mixing a bug fix with a refactor
  produces a confusing changelog entry
- The post-commit reviewer will warn if your diff exceeds 400 lines

---

## Branch Naming

```
{type}/{short-description}
```

Examples:
- `feat/churn-feature-pipeline`
- `infra/add-schema-validator-agent`
- `fix/null-customer-tier`

---

## What Gets Reviewed Automatically

On every commit containing Python files, the post-commit hook runs:

| Check | Tool | What it catches |
|---|---|---|
| Linting + formatting | ruff | Style, unused imports, syntax |
| Type checking | mypy | Type errors (Spark false positives ignored) |
| Security | bandit | Hardcoded secrets, unsafe patterns |
| Docstring coverage | interrogate | Missing docstrings on public functions |
| Tests | pytest | Failing tests in `tests/` |
| Code review | Claude Haiku | Spark anti-patterns, maintainability |
| Changelog | Qwen 3.5 9B | Structured entry added to CHANGELOG.md |

Reviews are **advisory only** — they never block a commit.
Reports are written to `.reviews/last-review.md`.

---

## Skipping the Hook

For genuinely urgent commits:

```bash
git commit --no-verify -m "chore: hotfix typo"
```

Use sparingly. `chore:` and `wip:` commits already skip the changelog
automatically — you rarely need `--no-verify`.

---

## Folder Structure Conventions

```
src/
  bronze/       ← ingestion only, no transformation
  silver/       ← cleaning, validation, deduplication
  gold/         ← business aggregations, feature tables
  utils/        ← shared helpers used across layers
  config/       ← centralised config, no hardcoded values
tests/          ← mirrors src/ structure
notebooks/      ← exploratory only, no production logic
.claude/
  agents/       ← agent definitions (commit these)
  skills/       ← skill definitions (commit these)
.githooks/      ← git hooks (commit these)
.reviews/       ← generated review reports (gitignore or commit)
docs/           ← business case, architecture, how-tos
```

---

## Python Conventions

- All public functions and classes must have docstrings (interrogate enforces this)
- Type hints required on all function signatures (mypy enforces this)
- No hardcoded paths, table names, or secrets — use `src/config/`
- No `SELECT *` in SQL or DataFrame `.select("*")`
- All `dbutils.widgets.get()` calls must have a default value
- Transformation logic belongs in `src/` not in notebooks

---

## Running the LinkedIn Agents

When you want to generate LinkedIn posts from recent changelog entries:

```bash
# General audience post
bash .githooks/linkedin-general

# Technical audience post
bash .githooks/linkedin-technical
```

Posts are written to `.posts/` and printed to terminal.
Always review and edit before publishing — the agent drafts, you publish.

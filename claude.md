## Project Context
- Python/Databricks data engineering project
- Medallion architecture: bronze → silver → gold
- PySpark and dbutils are available as globals — never flag as undefined




## Review Agents
Three review agents are available in .claude/agents/:
- de-code-reviewer — invoked automatically by pre-commit hook
- de-arch-reviewer — run manually before PRs or when refactoring
- de-biz-reviewer — run manually before releases, requires docs/business-case.md

## Conventions
- Tests live in tests/
- Business rules are documented in docs/business-case.md
- Review reports are written to .reviews/

## What not to do
- Do not suggest fixing mypy errors on pyspark/dbutils — these are expected
- Do not rewrite notebook logic into modules unless asked
---
name: databricks-conventions
description: |
  Databricks and PySpark best practices for a Python data engineering project.
  Load this skill when reviewing, analysing, or writing code that uses PySpark,
  dbutils, Delta Lake, or Databricks-specific APIs.
---

## Databricks Runtime Context

Code in this project runs on Databricks. The following are available as globals
in notebook contexts and should never be flagged as undefined or unimported:
- `spark` — active SparkSession
- `dbutils` — Databricks utilities (fs, secrets, widgets, notebook)
- `display()` — Databricks display function
- `sc` — SparkContext

Mypy will report these as errors. They are **expected false positives** and must
never be reported as real issues.

---

## PySpark Performance Anti-Patterns

Flag these when spotted in a diff:

| Anti-pattern | Why it matters | What to suggest |
|---|---|---|
| `.collect()` on large DataFrame | Pulls all data to driver, causes OOM | Use `.limit()` + `.collect()` or write to table |
| `df.count()` in a loop | Triggers a full scan each time | Cache the DataFrame first or restructure |
| Missing partition filter on large table | Full table scan, expensive | Add `WHERE partition_col = ...` |
| `crossJoin()` without explicit intent | Cartesian product, exponential cost | Verify intent, suggest broadcast join if one side is small |
| `udf()` instead of native Spark functions | UDFs break Catalyst optimisation | Suggest `pyspark.sql.functions` equivalent |
| `toPandas()` on unbounded DataFrame | Memory risk | Add `.limit()` or aggregate first |
| Repeated `spark.read` of same source | Re-reads from storage each time | Cache or persist the DataFrame |
| `repartition()` before write without reason | Unnecessary shuffle | Use `coalesce()` for reducing partitions |

---

## Delta Lake Patterns

**Good patterns to recognise and preserve:**
- `MERGE INTO` for upserts — preferred over overwrite for incremental loads
- `OPTIMIZE` + `ZORDER BY` for frequently filtered columns
- Schema evolution with `mergeSchema` option
- `vacuum` calls with a retention period

**Patterns to flag:**
- Overwriting a Delta table without checking for idempotency
- Reading a Delta table without specifying a version when reproducibility matters
- Missing `.option("overwriteSchema", "true")` when schema changes are intentional
- `insertInto()` without checking for duplicate keys

---

## dbutils Usage Patterns

**Flag these:**
```python
# Bad: widget value used without default
value = dbutils.widgets.get("param")

# Good: always provide a default
value = dbutils.widgets.get("param") if dbutils.widgets.getArgument("param", None) else "default"

# Bad: secret hardcoded
password = "my_secret_password"

# Good: use secrets
password = dbutils.secrets.get(scope="my-scope", key="db-password")

# Bad: mount operation without try/except
dbutils.fs.mount(source, mount_point)

# Good: guard mount operations
try:
    dbutils.fs.mount(source, mount_point)
except Exception as e:
    if "already mounted" not in str(e):
        raise
```

---

## Project Layer Conventions

This project follows a medallion architecture:

```
bronze/   — raw ingestion, no transformation, schema-on-read
silver/   — cleaned, validated, deduplicated
gold/     — business-level aggregations and feature tables
```

**Layer rules:**
- Bronze → Silver: validate schema, handle nulls, deduplicate
- Silver → Gold: apply business rules, aggregate, join
- Gold is read-only for consumers — no writes back to Silver from Gold logic
- Cross-layer imports: a module in `silver/` must not import from `gold/`

---

## Data Quality Expectations

Every pipeline reading from an external or bronze source should:
1. Validate schema on read (use `schema=` parameter or assert column presence)
2. Handle nulls explicitly — never silently propagate
3. Log record counts before and after major transformations
4. Define a deduplication strategy if the source can produce duplicates

Flag code that reads from bronze without any of these.

---

## Observability Patterns

**Good — flag absence of these in new pipelines:**
```python
import logging
logger = logging.getLogger(__name__)

logger.info(f"Reading from {source_table}, partition={partition_date}")
df = spark.read.table(source_table)
logger.info(f"Records read: {df.count()}")

# After transformation
logger.info(f"Records after dedup: {result_df.count()}")
logger.info(f"Writing to {target_table}")
```

**Flag when missing:**
- No logging in a function longer than 20 lines
- No record count logging around major joins or aggregations
- No error logging in except blocks (bare `except: pass` is always wrong)

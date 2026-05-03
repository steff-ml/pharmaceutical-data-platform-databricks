---
name: review-report-format
description: |
  Shared report formatting rules for all de-* reviewer agents.
  Load this skill whenever producing a code, architecture, or business logic
  review report. Defines structure, tone, length limits, and reference standards.
---

## Core Formatting Rules

1. **Total report length: 60 lines maximum** for code and arch reviews.
   Business logic reviews may run to 80 lines if many rules need checking.
2. **One issue per finding** — do not combine multiple problems into one bullet.
3. **Always cite file and line** for specific findings: `` `path/to/file.py:42` ``
4. **Never invent issues** not evidenced by tool output or the diff.
5. **Skip sections entirely** if they have nothing to report — do not write
   "No issues found" under every heading.

---

## Severity Levels

Use exactly these labels, in bold, at the start of each finding:

| Label | When to use |
|---|---|
| **CRITICAL** | Security vulnerability, data loss risk, hardcoded secret, broken pipeline |
| **ERROR** | Failing test, type error (non-Spark), missing required validation |
| **WARNING** | Linting issue, missing docstring on public function, performance anti-pattern |
| **INFO** | Suggestion, minor improvement, optional improvement |

---

## Status Icons

Use consistently in tool result tables:
- `✅` — tool passed (returncode 0)
- `❌` — tool failed (returncode 1+)
- `⏭️` — tool skipped or not installed

---

## Reference Standards

When suggesting learning resources, prefer in this order:
1. Official documentation (Databricks docs, PySpark API docs, PEP references)
2. Databricks engineering blog (databricks.com/blog)
3. Martin Fowler's refactoring catalogue (refactoring.guru or martinfowler.com)
4. Google Engineering Practices (google.github.io/eng-practices)
5. Specific book chapters: *Clean Code*, *Designing Data-Intensive Applications*,
   *The Pragmatic Programmer*

Format references as:
```markdown
- [Title](url) — one sentence on why it's relevant to this specific finding
```

Maximum 3 references per report. Only include references relevant to actual
findings — do not pad with generic links.

---

## Closing Line

Every report must end with exactly one of:
- `> Commit allowed. Review is advisory only.` — for pre-commit reports
- `> Advisory review. No commit gate.` — for manual arch/biz reviews

---

## Tone

- Direct and specific — name the file, the line, the pattern
- Not prescriptive about style unless a tool flagged it
- Constructive — every ERROR or WARNING gets a suggestion
- No filler phrases: avoid "Great job", "Overall this looks good",
  "As an AI language model", "It's worth noting that"

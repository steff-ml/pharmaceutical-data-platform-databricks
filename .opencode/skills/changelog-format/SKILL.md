---
name: changelog-format
description: |
  Defines the CHANGELOG.md structure used by the changelog writer and consumed
  by the LinkedIn post agents. Load this skill whenever reading or writing
  CHANGELOG.md to ensure consistent structure across all agents.
---

## File Location

`CHANGELOG.md` lives at the repository root. It is append-only — new entries
are prepended (newest first). Never delete or rewrite existing entries.

---

## Entry Structure

Each commit produces exactly one changelog entry in this format:

```markdown
## [{semantic_type}] {short_title} — {YYYY-MM-DD}

**Commit:** `{first 8 chars of commit hash}`
**Branch:** `{branch}`
**Files changed:** {comma-separated list of changed files}

### What changed
{2-4 sentences describing what was added, changed, or removed. Written in
plain past tense. No jargon. Focus on what, not how.}

### Why it matters
{1-2 sentences on the purpose or value of this change. Can reference a
business rule, a pipeline, or an engineering goal.}

### Technical detail
{1-3 bullet points of technical specifics. This is the layer LinkedIn
technical posts draw from. Include: patterns used, tools involved,
architectural decisions made.}

### Category
{One of: Infrastructure | Pipeline | Data Quality | Architecture |
Testing | Documentation | Security | Performance | Refactor}

---
```

---

## Semantic Types

Use exactly these prefixes — they match the commit convention in CONTRIBUTING.md:

| Commit prefix | Changelog type | LinkedIn relevance |
|---|---|---|
| `feat:` | `FEATURE` | High — always include |
| `fix:` | `FIX` | Medium — include if non-trivial |
| `refactor:` | `REFACTOR` | Medium — good for technical posts |
| `perf:` | `PERFORMANCE` | High — always include |
| `infra:` | `INFRASTRUCTURE` | High — captures tooling evolution |
| `docs:` | `DOCUMENTATION` | Low — include only if significant |
| `test:` | `TESTING` | Low — include only if new test strategy |
| `chore:` | skip entry | Do not write changelog entry |
| `wip:` | skip entry | Do not write changelog entry |

---

## Category Definitions

Used by LinkedIn agents to filter relevant entries:

- **Infrastructure** — hooks, agents, CI/CD, tooling, dev environment
- **Pipeline** — Databricks notebooks, ETL/ELT logic, scheduling
- **Data Quality** — validation, schema enforcement, deduplication
- **Architecture** — module structure, layer separation, design patterns
- **Testing** — test coverage, test strategies, conftest setup
- **Documentation** — CLAUDE.md, README, business case, how-tos
- **Security** — secrets handling, bandit findings, access patterns
- **Performance** — Spark optimisation, query tuning, caching
- **Refactor** — code restructuring without behaviour change

---

## Example Entry

```markdown
## [INFRASTRUCTURE] Added post-commit review hook with Claude Code — 2026-05-03

**Commit:** `c785bcd4`
**Branch:** `create_agents`
**Files changed:** `.githooks/post-commit`, `.claude/agents/de-code-reviewer.md`

### What changed
Added an automated post-commit hook that runs five static analysis tools
(ruff, mypy, bandit, interrogate, pytest) and sends the results to a Claude
Haiku agent for code review. The review report is written to
`.reviews/last-review.md` after every Python commit.

### Why it matters
Every Python commit now gets an instant quality gate without blocking the
commit or requiring manual review steps. The review is advisory but
surfaces real issues immediately.

### Technical detail
- Hook uses absolute venv paths to ensure tools resolve correctly in both
  Git Bash and VS Code shell environments
- Claude agent receives diff and tool output via temp files to avoid shell
  escaping issues with JSON payloads
- Agent system prompt injected via `--system-prompt` flag in `-p` mode

### Category
Infrastructure

---
```

---

## LinkedIn Agent Consumption

When LinkedIn agents read `CHANGELOG.md` they filter entries by:
- **General audience posts**: draw from `### What changed` and
  `### Why it matters` sections, category = Infrastructure | Pipeline |
  Feature | Performance
- **Technical audience posts**: draw from `### Technical detail` sections
  across all categories, plus `### What changed` for context

Agents should group related entries by theme rather than listing commits
chronologically — a post about "building a self-reviewing codebase" is more
compelling than "3 commits this week."

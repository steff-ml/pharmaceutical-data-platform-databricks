# Setup Claude Code review agents


## Setup Claude Code
(See docs)



## Setup review agents

### How Claude review agents work
Claude Code review agents are designed to support the development of high-quality code and implement hybrid-eyes (No code leaves the repo that hasn't been reviewed by both a human and an LLM to maximally leverage both strengths.)

Three review agents are available in .claude/agents/:
- de-code-reviewer — invoked automatically by pre-commit hook   
- de-arch-reviewer — run manually before PRs or when refactoring
- de-biz-reviewer — run manually before releases, requires docs/business-case.md

These agents share certain capabilities like databricks conventions understanding and report formatting. 
To enable this, three skills are defined in .claude/skills:
- business-context-loader: 
  Loads and interprets the project business case document for use in architecture and business logic reviews. Load this skill when a review requires understanding of business rules, pipeline intent, data contracts,or protected logic. Always read docs/business-case.md before producing any architecture or business logic findings.
- databricks-conventions: 
  Databricks and PySpark best practices for a Python data engineering project.
  Load this skill when reviewing, analysing, or writing code that uses PySpark,
  dbutils, Delta Lake, or Databricks-specific APIs.
- review-report: 
  Shared report formatting rules for all de-* reviewer agents.
  Load this skill whenever producing a code, architecture, or business logic
  review report. Defines structure, tone, length limits, and reference standards.

To run agents based on Git actions, .githooks are defined. Right now, we only have a pre-commit skill that runs code review prior to committing.
The other checks are done manually or when diffs are too large.

### One time Setup Claude Code agents

#### 1. Install Python dependencies

```bash
pip install ruff mypy pytest bandit interrogate
```

For Databricks/PySpark mypy stubs (prevents false positives):
```bash
pip install pyspark-stubs
```

#### 2. Register the hook

```powershell
git config core.hooksPath .githooks
icacls .githooks\post-commit /grant "*S-1-1-0:RX" 
```
icacls is for permission handling in powershell, the equivalent of chmod in Bash
#### 3. Add a mypy config (recommended)

Create `mypy.ini` in your repo root to suppress known Spark false positives:

```ini
[mypy]
ignore_missing_imports = True
warn_return_any = False

# Suppress false positives on Spark/Databricks internals
[mypy-pyspark.*]
ignore_errors = True

[mypy-databricks.*]
ignore_errors = True

[mypy-py4j.*]
ignore_errors = True
```

#### 4. Decide what to do with .reviews/

Option A — ignore generated reports (cleaner history):
```bash
echo ".reviews/" >> .gitignore
```

Option B — commit reports alongside code (useful for audit trail):
```bash
# Leave .reviews/ out of .gitignore
# Reports are committed automatically with each change

```
Option A is selected for clean history. The reviews are for me.
---

#### 5. Download jq
Option 2 — Manual download (if curl isn't available):

Go to https://github.com/jqlang/jq/releases/latest
Download jq-windows-amd64.exe
Rename it to jq.exe
Move it to C:\Program Files\Git\usr\bin\
### Customising the agent

Open `.claude/agents/de-code-reviewer.md` to:

- **Change the model**: swap `claude-haiku-4-5` for `claude-sonnet-4-6` at the top
  for deeper analysis (slower, uses more tokens)
- **Add custom checks**: add bullet points to the maintainability section
- **Adjust severity**: modify the severity definitions section
- **Add team conventions**: append a "Team conventions" section with your
  specific patterns (e.g. naming conventions, module layout rules)

---

### Skipping the hook for a commit

```bash
git commit --no-verify -m "your message"
```

Use this for WIP commits, merge commits, or when you're in a hurry.

---

### Diff size behaviour

| Diff size     | Behaviour                                      |
|---------------|------------------------------------------------|
| < 400 lines   | Full review — all tools + Claude               |
| 400–800 lines | Warning shown, full review still runs          |
| > 800 lines   | Tools run, Claude review skipped               |

To change these thresholds, edit `DIFF_WARN_THRESHOLD` and
`DIFF_SKIP_THRESHOLD` at the top of `.githooks/pre-commit`.

---

### Troubleshooting

**"claude CLI not found"**
Make sure Claude Code is installed and `claude` is on your PATH:
```bash
claude --version
```

**Mypy reports hundreds of Spark errors**
Add the `mypy.ini` config above — it suppresses PySpark false positives.

**Hook is too slow**
- Switch the agent model to `claude-haiku-4-5` (already the default)
- Reduce `TIMEOUT_SECONDS` in the hook script (default: 120)
- Use `git commit --no-verify` for rapid WIP commits

**pytest fails on import errors for Databricks modules**
Add a `conftest.py` in your `tests/` folder that mocks `dbutils` and `spark`:

```python
# tests/conftest.py
import sys
from unittest.mock import MagicMock

# Mock Databricks runtime modules so tests run locally
sys.modules['pyspark'] = MagicMock()
sys.modules['pyspark.sql'] = MagicMock()
sys.modules['databricks'] = MagicMock()
sys.modules['dbutils'] = MagicMock()
```
# Role: Claude Code Infrastructure Architect (Windows Specialist)
# Goal: Execute "Absolute Island" Automation for Python

Context: We are entering the "Automated Development" era.
Objective: Create a project where the Interpreter, Packages, and Cache are 100% contained within the current directory.

[Constraints for Claude Code]:

## 1. Use Global uv (Prefer System Installation)
- First check if `uv` is available globally: `uv --version`
- If available, use `uv` directly (no need to download)
- Only download standalone binary if `uv` is NOT in PATH

## 2. The "Island" Environment (The Core)
Before any execution, set the following environment variables for this session ONLY:
```bash
export UV_PYTHON_INSTALL_DIR="./.python_runtime"
export UV_CACHE_DIR="./.uv_cache"
export UV_PROJECT_ENVIRONMENT="./.venv"
```
This ensures that deleting this folder removes EVERY byte related to the project.

## 3. Automation Flow

### Step 1: Initialize Project
```bash
uv init --name <project_name>
```

### Step 2: Fix Python Version (CRITICAL!)
**IMPORTANT**: `uv init` may auto-detect the system Python and set `.python-version` incorrectly.
You MUST override these files:

**`.python-version`** (exact version):
```
3.12
```

**`pyproject.toml`** (pin version constraint):
```toml
[project]
name = "<project_name>"
version = "0.1.0"
requires-python = ">=3.12,<3.13"  # Pin to 3.12.x
dependencies = []
```

### Step 3: Install Python and Create Venv
```bash
uv python install 3.12
uv venv --python-preference only-managed
```

### Step 4: Update .gitignore
**IMPORTANT**: The auto-generated `.gitignore` is incomplete. Append these entries:
```gitignore
# Island Mode: Local Python runtime and cache
.python_runtime/
.uv_cache/
```

## 4. Persistence (The Command Center)
Generate a `CLAUDE.md` that enforces:
```markdown
# Project: <project_name> (Automated Island Mode)

## Always Use
uv run python <script.py>

## Never Use
- Global pip commands
- System Python directly

## Environment Variables (set before running)
UV_PYTHON_INSTALL_DIR=./.python_runtime
UV_CACHE_DIR=./.uv_cache
UV_PROJECT_ENVIRONMENT=./.venv

## Adding Dependencies
uv add <package-name>
```

## 5. Verification
```bash
uv run python -c "import sys; print(f'Path: {sys.executable}')"
```
Expected output: `.../project/.venv/Scripts/python.exe`

Also verify `.venv/pyvenv.cfg` contains:
```
home = .../project/.python_runtime/cpython-3.12-...
```

Confirm to user: "**Island Mode Active. Python 3.12.x is local. Ready for automated development.**"

## Common Issues & Fixes

| Issue | Cause | Fix |
|-------|-------|-----|
| venv uses system Python | `.python-version` wrong | Set to `3.12` |
| `uv run` recreates venv | `requires-python` too loose | Use `>=3.12,<3.13` |
| Wrong Python version in venv | Missing `--python-preference only-managed` | Add this flag |

---
Execute now for: [Your Project Name]

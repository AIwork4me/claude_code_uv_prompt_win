# Claude Code UV Prompt for Windows

A validated prompt that enables Claude Code to create fully isolated Python environments on Windows using [uv](https://github.com/astral-sh/uv).

## What is "Island Mode"?

**Island Mode** ensures that all Python-related files are 100% contained within your project directory:

| Component | Location |
|-----------|----------|
| Python Interpreter | `./.python_runtime/` |
| Package Cache | `./.uv_cache/` |
| Virtual Environment | `./.venv/` |

**Result**: Delete the project folder = Delete everything. No system pollution.

## Features

- Uses global `uv` if available (no redundant downloads)
- Locks Python version to 3.12.x (configurable)
- Prevents `uv run` from recreating venv with wrong Python
- Complete isolation from system Python
- Auto-generates `CLAUDE.md` for project persistence

## Prerequisites

1. **Claude Code CLI** - [Install Guide](https://docs.anthropic.com/en/docs/claude-code)
2. **uv** (optional but recommended) - [Install Guide](https://docs.astral.sh/uv/getting-started/installation/)
3. **Windows 10/11** with PowerShell or Git Bash

## Quick Start

### Option 1: Copy to CLAUDE.md (Recommended)

1. Copy `claude_uv_win.md` content to your project's `CLAUDE.md`
2. Replace `[Your Project Name]` with your actual project name
3. Start Claude Code in that directory

### Option 2: Use as a Prompt

Paste the content directly when starting a new Claude Code session:

```
Read the file at: path/to/claude_uv_win.md
Execute now for: my_project_name
```

### Option 3: Download Only

```bash
# Download the prompt file
curl -O https://raw.githubusercontent.com/aiwork4me/claude_code_uv_prompt_win/main/claude_uv_win.md
```

## Usage Example

After providing the prompt to Claude Code, it will:

```
1. Check for global uv installation
2. Initialize project with uv init
3. Fix .python-version to 3.12
4. Fix pyproject.toml to pin Python 3.12.x
5. Install Python 3.12 locally
6. Create venv with --python-preference only-managed
7. Update .gitignore
8. Generate CLAUDE.md for persistence
9. Verify everything works
```

### Expected Output

```
Python: 3.12.12
Path: C:\Users\...\my_project\.venv\Scripts\python.exe
Island Mode Active. Python 3.12.x is local. Ready for automated development.
```

## Verification

Check that your environment is properly isolated:

```bash
# Should point to local .venv
uv run python -c "import sys; print(sys.executable)"

# Check pyvenv.cfg - home should point to .python_runtime
cat .venv/pyvenv.cfg
```

## Project Structure

After running the prompt, your project will look like:

```
my_project/
├── .git/
├── .gitignore           # Includes .python_runtime/, .uv_cache/, .venv/
├── .python_runtime/     # Local Python 3.12.x
├── .python-version      # Contains: 3.12
├── .uv_cache/           # Local package cache
├── .venv/               # Virtual environment
├── CLAUDE.md            # Project rules for Claude Code
├── main.py
├── pyproject.toml       # requires-python = ">=3.12,<3.13"
└── uv.lock
```

## Why This Prompt?

### The Problem

When using `uv init` on Windows:
- It auto-detects system Python (e.g., 3.14)
- Sets `.python-version` to system version
- `uv run` may recreate venv with wrong Python
- No guarantee of complete isolation

### The Solution

This prompt enforces:
1. **Version Locking**: `.python-version = 3.12` + `requires-python = ">=3.12,<3.13"`
2. **Local Python**: `--python-preference only-managed` flag
3. **Environment Variables**: `UV_PYTHON_INSTALL_DIR`, `UV_CACHE_DIR`, `UV_PROJECT_ENVIRONMENT`
4. **Complete gitignore**: All isolation directories excluded

## Customization

### Change Python Version

Edit the prompt to use a different version:

```diff
- 3.12
+ 3.11

- requires-python = ">=3.12,<3.13"
+ requires-python = ">=3.11,<3.12"
```

### Use Local uv Binary

If you want to download uv into the project instead of using global:

```diff
- First check if `uv` is available globally
- If available, use `uv` directly
+ Download uv standalone binary into project root
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| venv uses system Python | Verify `.python-version` contains `3.12` |
| `uv run` recreates venv | Check `requires-python = ">=3.12,<3.13"` in pyproject.toml |
| Packages install globally | Ensure environment variables are set before running |

## Tested On

- Windows 11 Pro (10.0.26200)
- uv 0.10.4
- Python 3.12.12
- Claude Code CLI

## License

MIT License - feel free to use and modify.

## Contributing

Issues and pull requests are welcome at [GitHub](https://github.com/aiwork4me/claude_code_uv_prompt_win).

## Related

- [uv - Astral](https://github.com/astral-sh/uv)
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)

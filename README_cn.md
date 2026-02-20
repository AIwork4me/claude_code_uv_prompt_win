# Claude Code Windows 版 Python 环境隔离提示词

[English](README.md) | 中文

一个经过验证的提示词，让 Claude Code 在 Windows 上使用 [uv](https://github.com/astral-sh/uv) 创建完全隔离的 Python 环境。

## 什么是"岛屿模式"（Island Mode）？

**岛屿模式** 确保所有 Python 相关文件 100% 包含在您的项目目录中：

| 组件 | 位置 |
|------|------|
| Python 解释器 | `./.python_runtime/` |
| 包缓存 | `./.uv_cache/` |
| 虚拟环境 | `./.venv/` |

**结果**：删除项目文件夹 = 删除一切。不会污染系统。

## 特性

- 优先使用全局 `uv`（避免重复下载）
- 锁定 Python 版本到 3.12.x（可配置）
- 防止 `uv run` 使用错误的 Python 重建 venv
- 与系统 Python 完全隔离
- 自动生成 `CLAUDE.md` 保持项目规则

## 前置要求

1. **Claude Code CLI** - [安装指南](https://docs.anthropic.com/en/docs/claude-code)
2. **uv**（推荐）- [安装指南](https://docs.astral.sh/uv/getting-started/installation/)
3. **Windows 10/11** 搭配 PowerShell 或 Git Bash

## 如何使用

### 第一步：创建项目目录

```bash
mkdir my_project
cd my_project
```

### 第二步：复制提示词

复制 [claude_uv_win.md](claude_uv_win.md) 的全部内容

### 第三步：发送给 Claude Code

将提示词内容粘贴给 Claude Code，并附上您的项目名称：

```
<在此粘贴 claude_uv_win.md 的内容>

Execute now for: my_project
```

**就这样！** Claude Code 会自动处理一切。

### 第四步：验证（可选）

```bash
# 检查 Python 路径是否指向本地 .venv
uv run python -c "import sys; print(sys.executable)"

# 检查 .venv/pyvenv.cfg - home 应该指向 .python_runtime
cat .venv/pyvenv.cfg
```

预期输出：
```
Path: C:\...\my_project\.venv\Scripts\python.exe
home = C:\...\my_project\.python_runtime\cpython-3.12-...
```

## Claude Code 会做什么

收到提示词后，Claude Code 会自动执行以下步骤：

1. 检查全局 `uv` 安装
2. 使用 `uv init` 初始化项目
3. 修复 `.python-version` 为 `3.12`
4. 修复 `pyproject.toml` 锁定 Python 3.12.x
5. 在本地安装 Python 3.12
6. 使用 `--python-preference only-managed` 创建 venv
7. 更新 `.gitignore`
8. 生成 `CLAUDE.md` 用于持久化
9. 验证一切正常

## 项目结构

运行提示词后，您的项目结构如下：

```
my_project/
├── .git/
├── .gitignore           # 包含 .python_runtime/, .uv_cache/, .venv/
├── .python_runtime/     # 本地 Python 3.12.x
├── .python-version      # 内容：3.12
├── .uv_cache/           # 本地包缓存
├── .venv/               # 虚拟环境
├── CLAUDE.md            # Claude Code 项目规则
├── main.py
├── pyproject.toml       # requires-python = ">=3.12,<3.13"
└── uv.lock
```

## 为什么需要这个提示词？

### 问题

在 Windows 上使用 `uv init` 时：
- 自动检测系统 Python（如 3.14）
- 将 `.python-version` 设置为系统版本
- `uv run` 可能使用错误的 Python 重建 venv
- 无法保证完全隔离

### 解决方案

这个提示词强制执行：
1. **版本锁定**：`.python-version = 3.12` + `requires-python = ">=3.12,<3.13"`
2. **本地 Python**：`--python-preference only-managed` 标志
3. **环境变量**：`UV_PYTHON_INSTALL_DIR`、`UV_CACHE_DIR`、`UV_PROJECT_ENVIRONMENT`
4. **完整的 gitignore**：排除所有隔离目录

## 自定义

### 更改 Python 版本

编辑提示词以使用不同版本：

```diff
- 3.12
+ 3.11

- requires-python = ">=3.12,<3.13"
+ requires-python = ">=3.11,<3.12"
```

## 常见问题

| 问题 | 解决方案 |
|------|----------|
| venv 使用系统 Python | 验证 `.python-version` 包含 `3.12` |
| `uv run` 重建 venv | 检查 pyproject.toml 中的 `requires-python = ">=3.12,<3.13"` |
| 包安装到全局 | 确保运行前设置了环境变量 |

## 测试环境

- Windows 11 Pro (10.0.26200)
- uv 0.10.4
- Python 3.12.12
- Claude Code CLI

## 许可证

MIT 许可证 - 可自由使用和修改。

## 贡献

欢迎在 [GitHub](https://github.com/aiwork4me/claude_code_uv_prompt_win) 提交问题和拉取请求。

## 相关链接

- [uv - Astral](https://github.com/astral-sh/uv)
- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)

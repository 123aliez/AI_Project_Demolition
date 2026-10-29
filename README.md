# AI Project Demolition：AI 驱动的项目开发管理框架

一套面向 AI 编码助手（Claude Code / Codex CLI 等）的**项目开发管理框架**。AI 在会话开始时就掌握工作区里有哪些项目、各自的开发约定和进度。开发采用 PM 模式：Claude（Opus）负责规划和验收，Codex 负责写代码、读代码和跑测试。

## 核心理念

- **上下文预加载**：`CLAUDE.md` 用 `@` 导入规则文件，会话启动时自动载入，不需要人工解释环境
- **PM 模式**：Claude 写任务书、验收，不直接改代码；Codex 执行。验收时另开一个 Codex 会话做独立审查
- **先确认再动手**：任何文件修改都先给出修改计划，委派给 Codex 的任务书就是修改计划
- **硬约束**：`.claude/settings.json` 按扩展名禁止 Claude 直接编辑代码文件，全权限启动时同样生效
- **工具无关**：规则写在纯 Markdown 文件里，Claude Code 和 Codex 通用
- **跨会话持久**：项目状态写入 `PROJECT.md` / `PROGRESS.md`，不依赖 AI 的记忆系统

## 目录结构

```
<工作区>/
├── CLAUDE.md              # Claude Code 入口（@ 导入规则文件）
├── AGENTS.md              # Codex 入口（含执行者模式）
├── RULES_GLOBAL.md        # 行为规则（第 7 条为 PM 模式）
├── DESC_DIR.md            # 目录与项目管理规范
├── DESC_ENV.md            # 运行环境与 AI 工具链
├── PROJECTS_INDEX.md      # 全局项目索引
├── PROJECTS_LOCAL.md      # 本机外部项目索引（不同步）
├── .claude/settings.json  # 权限：允许调用 codex，禁止 Claude 直接改代码文件
├── .gitignore
├── _template/
│   ├── PROJECT.md         # 项目文档模板（含"开发约定"）
│   └── PROGRESS.md        # 进度追踪模板
├── plan/                  # 构思与架构文档（默认不读）
├── archive/               # 已完成或弃用的项目
└── YYYYMMDD_项目名/        # 各项目文件夹，每个都是独立的 git 仓库
```

## 文件说明

| 文件 | 说明 |
|------|------|
| `CLAUDE.md` | 用 `@` 导入四个规则文件，本身不写任何状态 |
| `AGENTS.md` | Codex 的入口。提示词以「【任务书】」开头时进入执行者模式：跳过初始化和确认流程，只在授权范围内改动 |
| `RULES_GLOBAL.md` | 语言、先确认再动手、握手、状态存储唯一性、网络代理、PM 模式等规则 |
| `DESC_DIR.md` | 项目寻址、新项目创建（`YYYYMMDD_项目名`，软件项目独立 git）、台账维护、操作红线、模板机制 |
| `DESC_ENV.md` | Linux / bash 环境约束和 AI 工具链 |
| `_template/PROJECT.md` | 软件项目必须填写"开发约定"（技术栈、测试命令、禁区），Codex 每次开工前都会读取 |

## 配套工具

PM 模式依赖下面两个仓库：

| 工具 | 仓库 | 作用 |
|------|------|------|
| codexmcp | https://github.com/123aliez/codexmcp | MCP 服务，让 Claude Code 调用本机的 Codex CLI；支持并行调用、超时，返回改动文件和执行命令等证据 |
| codex-delegate | https://github.com/123aliez/codex-delegate | Claude Code skill，提供任务书模板、验收清单和独立审查流程；第 0～6 节是本框架用到的开发场景 |

```bash
# 注册 codexmcp（user 级，名称必须是 codex）。需要代理时加 -e HTTPS_PROXY=... -e HTTP_PROXY=...
claude mcp add codex -s user --transport stdio -- uvx --from git+https://github.com/123aliez/codexmcp.git codexmcp

# 安装 skill（私有仓库，需要有访问权限的账号）
git clone https://github.com/123aliez/codex-delegate.git ~/.claude/skills/codex-delegate

# 以后更新 skill
git -C ~/.claude/skills/codex-delegate pull
```

## 快速开始

### 1. 准备工作区

把本仓库的文件复制到你的工作区根目录。注意要带上隐藏文件 `.claude/settings.json` 和 `.gitignore`；工作区里已有 `PROJECTS_INDEX.md` 和 `PROJECTS_LOCAL.md` 的话，不要覆盖。

### 2. 按需调整

- `DESC_ENV.md`：环境说明和 AI 工具链。
- `.claude/settings.json`：禁止 Claude 直接编辑的代码扩展名。非软件项目需要 Claude 直接写 `html`、`css`、`sh` 这类文件时，删掉对应的行。

### 3. 启动

```bash
cd <工作区>
claude
```

- 首次启动时在提示中选择信任该目录，`allow` 规则才会生效（全权限启动时不需要）。
- 执行 `/model opus`。
- 用 `/mcp` 确认 codex 已连接；输入 `/`，能看到 `codex-delegate`。

### 4. 开始开发

直接描述需求即可。AI 会按规则确认项目归属，或新建 `YYYYMMDD_项目名` 文件夹并初始化 git；然后写任务书交给你确认，再委派给 Codex 执行，最后完成验收和独立审查。

## 与 [ai-linux-ops](https://github.com/123aliez/ai-linux-ops) 的关系

本框架是**开发版**，`ai-linux-ops` 是**运维版**，两者平行独立：

| | AI Project Demolition（开发版） | ai-linux-ops（运维版） |
|---|---|---|
| 管理对象 | 开发项目 | 部署服务 |
| 索引文件 | PROJECTS_INDEX.md | SERVICES_INDEX.md |
| 文档模板 | PROJECT.md / PROGRESS.md | SERVICE.md / PROGRESS.md |
| Codex 的角色 | 执行者（写代码、跑测试），另开会话做独立审查 | 只读助手（信息收集、方案审查、完成审查） |
| 典型场景 | 写代码、修 Bug、重构 | 部署服务、排障、配置变更 |

---

# English

# AI Project Demolition — AI-Powered Project Development Framework

A **project development management framework** for AI coding assistants (Claude Code / Codex CLI, etc.). At session start the AI already knows which projects live in the workspace, along with each project's conventions and progress. Development follows a PM model: Claude (Opus) plans and accepts work, and Codex writes code, reads code and runs tests.

## Core Principles

- **Context Pre-loading**: `CLAUDE.md` `@`-imports the rule files, so they load automatically at session start
- **PM Model**: Claude writes task specs and accepts work but never edits code directly; Codex executes. A separate Codex session independently reviews each change
- **Confirm Before Acting**: every file change starts with a plan for the user to confirm; a task spec for Codex is that plan
- **Hard Guardrails**: `.claude/settings.json` blocks Claude from editing code files by extension, including in full-permission mode
- **Tool-Agnostic**: rules are plain Markdown, shared by Claude Code and Codex
- **Cross-Session Persistence**: project state lives in `PROJECT.md` / `PROGRESS.md`, independent of AI memory

## Directory Structure

```
<workspace>/
├── CLAUDE.md              # Claude Code entry (@-imports the rule files)
├── AGENTS.md              # Codex entry (with executor mode)
├── RULES_GLOBAL.md        # Behavior rules (rule 7 is the PM model)
├── DESC_DIR.md            # Directory and project management rules
├── DESC_ENV.md            # Environment and AI toolchain
├── PROJECTS_INDEX.md      # Global project index
├── PROJECTS_LOCAL.md      # Local external projects (not synced)
├── .claude/settings.json  # Permissions: allow codex, block direct code edits
├── .gitignore
├── _template/
│   ├── PROJECT.md         # Project doc template (with "dev conventions")
│   └── PROGRESS.md        # Progress tracking template
├── plan/                  # Ideas and architecture drafts (not read by default)
├── archive/               # Finished or abandoned projects
└── YYYYMMDD_name/         # Project folders, each its own git repository
```

## Companion Tools

The PM model relies on two repositories:

| Tool | Repository | Purpose |
|------|------------|---------|
| codexmcp | https://github.com/123aliez/codexmcp | MCP server that lets Claude Code call the local Codex CLI; supports parallel calls and timeouts, and returns evidence such as changed files and executed commands |
| codex-delegate | https://github.com/123aliez/codex-delegate | Claude Code skill with task-spec templates, acceptance checklists and independent review; sections 0–6 cover the development scenario this framework uses |

```bash
# Register codexmcp (user scope; the name must be codex). Add -e HTTPS_PROXY=... -e HTTP_PROXY=... if you need a proxy
claude mcp add codex -s user --transport stdio -- uvx --from git+https://github.com/123aliez/codexmcp.git codexmcp

# Install the skill (private repository; needs an account with access)
git clone https://github.com/123aliez/codex-delegate.git ~/.claude/skills/codex-delegate

# Update the skill later
git -C ~/.claude/skills/codex-delegate pull
```

## Quick Start

1. Copy this repository's files into your workspace root, including the hidden `.claude/settings.json` and `.gitignore`. Don't overwrite an existing `PROJECTS_INDEX.md` or `PROJECTS_LOCAL.md`.
2. Adjust `DESC_ENV.md` and, if needed, the blocked extensions in `.claude/settings.json`.
3. Run `claude` in the workspace root. Trust the folder on first start (so `allow` rules apply), run `/model opus`, and check that `/mcp` shows codex connected and `/` lists `codex-delegate`.
4. Describe what you want. The AI finds or creates the project folder, writes a task spec for you to confirm, delegates it to Codex, then accepts the result and runs an independent review.

## Relationship with [ai-linux-ops](https://github.com/123aliez/ai-linux-ops)

This framework is the **dev edition**; `ai-linux-ops` is the **ops edition**. They run in parallel and independently: here Codex is the executor (with a separate review session), while in ai-linux-ops Codex is a read-only assistant for information gathering and reviews.

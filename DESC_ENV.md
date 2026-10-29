<desc_env>
# 当前系统与开发环境配置

1. **操作系统**：Linux。发行版与版本以 `cat /etc/os-release` 的输出为准。
2. **终端约束**：所有命令使用 Linux / bash 语法，禁止给出 Windows PowerShell 或 cmd 命令。
3. **核心环境版本**：不在此硬编码，需要时用命令实时查询，例如 `git --version`、`python3 --version`、`node --version`、`uv --version`。
4. **环境隔离**：在提供 Python 或 Node.js 的安装指令时，默认考虑虚拟环境（如 `venv`、`uv`）或本地项目安装，除非我明确要求全局安装。
5. **AI 工具链**：
   - **Claude Code**：主控（PM 角色），主模型 Opus。
   - **Codex CLI**：执行者，经 codexmcp 调用。版本用 `codex --version`，登录状态用 `codex login status` 查询。
   - **codexmcp**：`https://github.com/123aliez/codexmcp`（PM 工作流 fork），以 user 级 MCP 注册，名称为 `codex`，由 `uvx` 启动。
</desc_env>

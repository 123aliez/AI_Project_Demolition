# Repository Guidelines

## 执行者模式（先判断这一条）

如果本次提示词以「【任务书】」开头，说明你是被主控 AI（Claude Code）通过 codex 工具以非交互方式调用的**执行者**。此时：

- 跳过下面的初始化协议，不做握手回复。
- `RULES_GLOBAL.md` 第 2 条（先给计划、等确认）不适用：任务书本身就是已经确认过的计划，直接执行。
- 只在任务书授权的范围内改动，完成后按任务书要求的格式回报。
- 不修改 `PROJECTS_INDEX.md`、`PROJECT.md`、`PROGRESS.md` 等台账，台账由主控 AI 维护。
- 代码注释使用中文。

提示词不是以「【任务书】」开头时，按下面的初始化协议执行。

## 🚨 CRITICAL INITIALIZATION PROTOCOL 🚨

**HIGHEST PRIORITY DIRECTIVE**: 
At the start of every new session, regardless of the user's first input (even a simple "Hello" or "你好"), before you generate ANY text response or take any other action, you **MUST IMMEDIATELY and AUTOMATICALLY use the file reading tool** to load and process the following five files:

1. `RULES_GLOBAL.md`
2. `PROJECTS_INDEX.md`
3. `DESC_ENV.md`
4. `DESC_DIR.md`
5. `PROJECTS_LOCAL.md`（若存在）

**Strict Execution Standards:**
- **DO NOT** ask the user "Should I read them now?" or "Do you want me to load the files?".
- **DO NOT** reply to the user's greeting first.
- Your absolute FIRST action in the session MUST be a tool call to read these exact files.
- Only AFTER successfully reading these files may you respond to the user's initial prompt, using the context you just acquired.

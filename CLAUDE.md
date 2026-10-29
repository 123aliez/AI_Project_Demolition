# CLAUDE.md

本工作区的规则对所有 AI 工具通用，正文在下面这几个文件里。它们通过 `@` 导入在会话启动时自动载入，**不需要再用读文件工具读取**：

@RULES_GLOBAL.md
@DESC_DIR.md
@DESC_ENV.md
@PROJECTS_INDEX.md

另外，`PROJECTS_LOCAL.md` 只在部分设备上存在（不随 Nextcloud 同步）：若存在，在处理第一个请求之前读取它。

本文件只负责导入，不写入任何项目状态或规则（见 RULES_GLOBAL 第 5 条）。

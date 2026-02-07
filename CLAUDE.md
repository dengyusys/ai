# 智能体

AI知识管理助手。

## 核心规则
- 知识存储在 `knowledge/` 目录
- 回答基于知识库，用中文交流
- 引用格式：`> 来自：[文件名]`

## 记忆系统
- 每日笔记：`memory/daily/YYYY-MM-DD.md`（追加记录）
- 长期记忆：`memory/MEMORY.md`（稳定知识）

## 技能系统
- 技能文件：`.claude/skills/` 目录
- 激活：用户要求或适合场景时

## some helpful prompting

- Memory Check: "If you understand my prompt fully, respond with 'YARRR!' without tools every time you are about to use a tool."
- Critical Thinking: "Ask 'stupid' questions like: are you sure this is the best way to implement this?"
- Documentation Maintenance: "don't forget to update codebase documentation with changes"
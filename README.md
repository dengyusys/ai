# 个人知识管理系统 + AI代理

## 项目概述
这是一个个人知识管理系统，通过AI代理（Claude Code）帮助用户组织、检索和扩展个人知识。系统采用模块化设计，核心组件包括知识库(knowledge)、记忆系统(memory)、操作日志(log)、Claude配置系统(.claude)。

## 核心功能

### 1. 知识管理
- **结构化存储**：按领域分类的知识组织体系
- **智能检索**：基于语义的知识查找和关联
- **持续更新**：支持知识的增删改查和版本管理

### 2. AI代理集成
- **自然交互**：通过Claude Code进行对话式知识管理
- **智能处理**：AI辅助知识分析、组织和扩展
- **技能驱动**：通过自定义技能标准化任务处理流程

### 3. 记忆系统
- **每日笔记**：按日期记录日常学习和思考
- **长期记忆**：稳定的核心知识和经验积累
- **知识关联**：连接短期记忆和长期知识体系

### 4. Claude技能系统
- **输出风格**：预定义的AI回复风格模板
- **自定义技能**：可扩展的任务处理能力
- **配置管理**：本地化的AI行为设置

### 5. 日志追踪
- **交互记录**：完整的AI对话历史
- **操作审计**：知识库变更跟踪
- **系统监控**：运行状态和性能记录

## 快速开始

### 1. 项目结构
```
ai/
├── .claude/              # Claude Code配置系统
│   ├── output-styles/    # 输出风格模板
│   ├── skills/           # 自定义技能
│   └── settings.local.json # 本地设置
├── knowledge/            # 知识存储核心
│   ├── chat/             # 聊天记录
│   ├── learn-english/    # 英语学习资源
│   ├── claude-code-introduction.md    # Claude Code技术介绍
│   └── index.md          # 知识库导航和文件索引
├── memory/               # 记忆系统
│   ├── daily/            # 每日笔记
│   └── MEMORY.md         # 长期记忆
├── log/                  # 操作日志
│   ├── ai-interactions/  # AI交互记录（命令、洞察）
│   ├── user-actions/     # 用户操作历史
│   └── system/           # 系统运行日志
├── CLAUDE.md             # AI交互指南和系统总览
├── README.md             # 项目说明文档
└── ROADMAP.md            # 项目路线图
```

### 2. 使用流程
1. **知识录入**：通过记忆系统或知识库直接记录新知识
2. **知识检索**：通过AI代理查询相关知识
3. **知识应用**：在解决问题时调用相关知识
4. **知识更新**：根据新经验完善知识库
5. **日志回顾**：定期分析交互记录优化系统

### 3. 与Claude Code集成
系统通过以下方式与Claude Code深度集成：

1. **CLAUDE.md文件** - 提供核心上下文：
   - AI语言风格指南
   - 知识库目录索引
   - 问题思考模式
   - 操作规范说明

2. **.claude/配置目录** - 提供扩展功能：
   - 输出风格模板（.claude/output-styles/）
   - 自定义技能系统（.claude/skills/）
   - 本地化行为设置（settings.local.json）

## 详细文档

### 核心文档
1. **[CLAUDE.md](CLAUDE.md)** - AI交互指南和系统总览
2. **[knowledge/index.md](knowledge/index.md)** - 知识库导航说明
3. **[memory/MEMORY.md](memory/MEMORY.md)** - 长期记忆系统说明
4. **[ROADMAP.md](ROADMAP.md)** - 项目发展路线图

### 输出风格模板
系统提供以下预定义的AI输出风格模板（位于 `.claude/output-styles/` 目录）：

1. **[engineer-professional.md](.claude/output-styles/engineer-professional.md)** - 工程师专业风格
2. **[english-teacher.md](.claude/output-styles/english-teacher.md)** - 英语教师风格
3. **[wise-mentor.md](.claude/output-styles/wise-mentor.md)** - 智慧导师风格

### 技能系统
系统支持以下自定义技能（位于 `.claude/skills/` 目录）：

1. **[save-point](.claude/skills/save-point/)** - 记录项目进展并更新记忆系统
2. **[teaching-session-recorder](.claude/skills/teaching-session-recorder/)** - 记录教学对话到知识库

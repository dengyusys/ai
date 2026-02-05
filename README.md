# 个人知识管理系统 + AI代理

## 项目概述
这是一个个人知识管理系统，通过AI代理（Claude Code）帮助用户组织、检索和扩展个人知识。系统采用模块化设计，核心组件包括知识库(knowledge)、模板系统(template)、操作日志(log)。

## 核心功能

### 1. 知识管理
- **结构化存储**：按领域分类的知识组织体系
- **智能检索**：基于语义的知识查找和关联
- **持续更新**：支持知识的增删改查和版本管理

### 2. AI代理集成
- **自然交互**：通过Claude Code进行对话式知识管理
- **智能处理**：AI辅助知识分析、组织和扩展
- **模板驱动**：标准化的问题处理和回答流程

### 3. 模板系统
- **对话模板**：标准化的问题解决框架
- **知识模板**：统一的知识记录格式
- **输出模板**：规范的报告和总结格式

### 4. 日志追踪
- **交互记录**：完整的AI对话历史
- **操作审计**：知识库变更跟踪
- **系统监控**：运行状态和性能记录

## 快速开始

### 1. 项目结构
```
ai/
├── CLAUDE.md              # AI交互指南和系统总览
├── knowledge/             # 知识存储核心
│   ├── areas/            # 领域知识（技术、工作、学习等）
│   ├── notes/            # 日常笔记和想法
│   ├── references/       # 参考资料和收藏
│   ├── projects/         # 项目文档
│   └── templates/        # 知识记录模板
├── templates/            # 模板系统
│   ├── conversation/     # 对话模板
│   ├── knowledge-format/ # 知识结构模板
│   └── output-format/    # 输出格式模板
└── log/                  # 操作日志
    ├── ai-interactions/  # AI交互记录
    ├── user-actions/     # 用户操作历史
    └── system/           # 系统运行日志
```

### 2. 使用流程
1. **知识录入**：使用模板记录新知识
2. **知识检索**：通过AI代理查询相关知识
3. **知识应用**：在解决问题时调用相关知识
4. **知识更新**：根据新经验完善知识库
5. **日志回顾**：定期分析交互记录优化系统

### 3. 与Claude Code集成
系统通过CLAUDE.md文件为Claude Code提供上下文：
- AI语言风格指南
- 知识库目录索引
- 问题思考模式
- 操作规范说明

## 详细文档

### 核心文档
1. **[CLAUDE.md](CLAUDE.md)** - AI交互指南和系统总览
2. **[knowledge/index.md](knowledge/index.md)** - 知识库导航说明
3. **[templates/README.md](templates/README.md)** - 模板系统说明
4. **[log/README.md](log/README.md)** - 日志系统说明

### 模板文档
1. **对话模板**：
   - [qa-patterns.md](templates/conversation/qa-patterns.md) - 问答模式
   - [brainstorming.md](templates/conversation/brainstorming.md) - 头脑风暴
   - [analysis-frames.md](templates/conversation/analysis-frames.md) - 分析框架

2. **知识模板**：
   - [note-structure.md](templates/knowledge-format/note-structure.md) - 笔记结构

3. **输出模板**：
   - [summary.md](templates/output-format/summary.md) - 总结报告

## 使用示例

### 示例1：学习新知识
```markdown
用户：请帮我学习React Hooks的核心概念
AI代理：
1. 使用qa-patterns.md中的"定义类问题"模板
2. 检索knowledge/areas/technology/react/相关知识
3. 结合note-structure.md模板组织学习笔记
4. 记录交互到log/ai-interactions/conversations/
```

### 示例2：解决问题
```markdown
用户：项目构建失败，错误信息是...
AI代理：
1. 使用analysis-frames.md中的"根本原因分析"框架
2. 查找类似问题的解决记录
3. 提供分步骤的解决方案
4. 将解决方案存入knowledge/areas/technology/build-issues/
```

### 示例3：创意生成
```markdown
用户：关于个人知识管理有什么创新想法？
AI代理：
1. 使用brainstorming.md模板引导思考
2. 关联不同领域的相关知识
3. 生成结构化的创意提案
4. 记录创意到knowledge/notes/ideas/
```

## 配置指南

### 1. Claude Code配置
确保Claude Code有权限访问项目目录，并了解CLAUDE.md中的指导原则。

### 2. 知识库初始化
根据个人需求调整knowledge/目录结构，创建初始知识内容。

### 3. 模板定制
根据使用习惯修改或添加模板，保持模板的实用性和一致性。

### 4. 日志设置
配置日志级别、保留策略和存储位置。

## 最佳实践

### 1. 知识组织
- 新知识及时归类存储
- 建立知识点之间的关联
- 定期回顾和整理旧知识

### 2. AI交互
- 提供充分的上下文信息
- 使用结构化的问题描述
- 接受并完善AI的建议

### 3. 系统维护
- 定期备份重要知识
- 分析日志优化交互
- 更新模板适应新需求

## 发展路线

### 短期目标
- [x] 基础系统框架搭建
- [x] 核心模板创建
- [ ] 与Claude Code深度集成测试
- [ ] 创建更多示例知识内容

### 中期目标
- [ ] 实现知识关联可视化
- [ ] 开发简单的CLI管理工具
- [ ] 添加知识质量评估功能
- [ ] 支持多用户协作

### 长期愿景
- [ ] 智能知识推荐系统
- [ ] 多模态知识支持
- [ ] 自动化知识摘要生成
- [ ] 跨平台同步和分享

## 贡献与反馈

这是一个个人知识管理系统原型，欢迎基于此思路进行定制和扩展。如果您有改进建议或使用经验，欢迎分享。

## 许可证
个人使用，根据需要进行修改和扩展。
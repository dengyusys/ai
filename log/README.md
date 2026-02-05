# 日志系统说明

## 概述
个人知识管理系统的日志记录模块，用于跟踪AI交互、用户操作和系统运行状态。

## 日志目录结构

### 1. AI交互日志 (ai-interactions/)
记录所有与AI代理的交互过程：
- **conversations/** - 完整对话记录
- **commands/** - 执行命令记录
- **insights/** - AI生成的洞察和建议

### 2. 用户操作日志 (user-actions/)
记录用户对知识库的操作：
- **edits/** - 知识内容的编辑记录
- **searches/** - 搜索历史和结果
- **imports/** - 外部知识导入记录

### 3. 系统日志 (system/)
记录系统运行状态：
- **performance/** - 性能指标和响应时间
- **errors/** - 错误和异常记录

## 日志格式规范

### 基础日志格式
```json
{
  "timestamp": "YYYY-MM-DDTHH:MM:SS.sssZ",
  "level": "info|warn|error|debug",
  "module": "knowledge|template|log|api",
  "action": "具体操作名称",
  "user": "用户标识",
  "session": "会话ID",
  "details": {
    "操作详情": "具体内容",
    "参数": "相关参数",
    "结果": "操作结果"
  },
  "duration": 123, // 操作耗时(ms)
  "status": "success|failure|partial"
}
```

### 对话日志格式
```json
{
  "timestamp": "YYYY-MM-DDTHH:MM:SS.sssZ",
  "session": "会话ID",
  "type": "question|answer|command|insight",
  "content": "具体内容",
  "context": {
    "previous_turns": ["历史对话"],
    "knowledge_references": ["引用的知识"],
    "templates_used": ["使用的模板"]
  },
  "metadata": {
    "response_time": 1234,
    "token_count": 567,
    "ai_model": "claude-3-5-sonnet"
  }
}
```

## 日志记录策略

### 1. 记录级别
- **info**：常规操作记录
- **warn**：需要注意的情况
- **error**：错误和异常
- **debug**：调试信息（开发时使用）

### 2. 保留策略
- 对话日志：保留30天
- 操作日志：保留90天
- 系统日志：保留180天
- 错误日志：永久保留

### 3. 存储策略
- 当前日志：文本文件存储
- 历史日志：压缩归档
- 重要日志：定期备份

## 日志分析

### 1. 使用频率分析
- 最常访问的知识领域
- 最常用的模板类型
- 最高频的搜索关键词

### 2. 性能分析
- 平均响应时间
- 常见错误类型
- 系统瓶颈识别

### 3. 知识增长分析
- 新增知识数量
- 知识更新频率
- 知识关联密度

## 日志查询

### 1. 时间范围查询
```bash
# 查看最近7天的对话日志
find ai-interactions/conversations -name "*.json" -mtime -7

# 查看特定日期的操作日志
find user-actions/edits -name "*2024-01-15*.json"
```

### 2. 关键词搜索
```bash
# 搜索包含特定关键词的日志
grep -r "关键词" ai-interactions/

# 搜索错误日志
grep -r "\"level\": \"error\"" system/errors/
```

### 3. 统计分析
```bash
# 统计各模块日志数量
find . -name "*.json" | awk -F/ '{print $2}' | sort | uniq -c

# 统计错误频率
grep -r "\"level\": \"error\"" . | wc -l
```

## 隐私与安全

### 1. 敏感信息处理
- 用户身份信息脱敏
- API密钥等机密信息不记录
- 个人隐私内容加密存储

### 2. 访问控制
- 日志文件权限设置为仅所有者可读写
- 压缩归档文件加密存储
- 定期清理临时日志文件

### 3. 合规要求
- 符合数据保护法规
- 用户有权访问自己的日志
- 支持日志导出和删除

## 维护指南

### 1. 日常维护
- 每日检查日志文件大小
- 每周压缩归档旧日志
- 每月分析日志统计数据

### 2. 故障处理
- 日志写入失败时切换到备用存储
- 存储空间不足时自动清理旧日志
- 日志损坏时尝试修复或重建

### 3. 性能优化
- 大量日志时启用分片存储
- 频繁查询时建立索引
- 长期存储时使用压缩格式
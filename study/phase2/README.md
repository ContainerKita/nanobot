# 第二阶段：核心深入

> 完成第一阶段后开始本阶段学习

## 章节列表

### [第 8 章：工具系统深入 - 文件/Shell 工具](./chapter08.md)
- 深入剖析 ReadFile、WriteFile、EditFile 实现
- 理解 Shell 工具的安全限制
- 学习参数校验机制

### [第 9 章：会话管理与上下文构建](./chapter09.md)
- Session 的生命周期管理
- ContextBuilder 如何组装 Prompt
- 历史消息的截断策略

### [第 10 章：记忆系统与向量检索](./chapter10.md)
- MemoryStore 的实现原理
- 长短期记忆的区分
- 向量检索（如果启用）

### [第 11 章：Cron 定时任务系统](./chapter11.md)
- cron 表达式解析（croniter）
- CronService 的任务调度
- 如何添加定时任务

### [第 12 章：MCP 协议与外部工具集成](./chapter12.md)
- MCP (Model Context Protocol) 简介
- 如何连接 MCP 服务器
- 自定义 MCP 工具

### [第 13 章：配置系统与 Pydantic 校验](./chapter13.md)
- pydantic BaseSettings 深入
- Field 校验器和转换器
- 环境变量映射技巧

### [第 14 章：异步编程模式与并发控制](./chapter14.md)
- asyncio 核心概念复习
- gather vs wait vs as_completed
- AsyncExitStack 的资源管理

---

**完成后进入：**[里程碑 2 测试](./milestone02.md)

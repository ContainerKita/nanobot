# 第三阶段：扩展实战

> 完成第二阶段后开始本阶段学习

## 章节列表

### [第 15 章：创建自定义 Tool](./chapter15.md)
**实战项目：**开发一个"系统监控工具"
- 继承 Tool 基类
- 定义 JSON Schema
- 实现 execute 方法
- 注册和测试

### [第 16 章：接入新的 Channel](./chapter16.md)
**实战项目：**实现一个 HTTP Webhook Channel
- 继承 BaseChannel
- 实现 start/stop
- 处理入站/出站消息
- 测试和调试

### [第 17 章：添加新 Provider](./chapter17.md)
**实战项目：**接入 Ollama 本地模型
- 继承 LLMProvider
- 实现 chat 方法
- 处理流式响应
- 注册到 ProviderRegistry

### [第 18 章：开发 Skill 技能包](./chapter18.md)
**实战项目：**创建"服务器巡检 Skill"
- Skill 的目录结构
- SKILL.md 文档规范
- 集成自定义工具和 Prompt

### [第 19 章：测试编写与调试技巧](./chapter19.md)
- pytest 基础
- 异步测试（pytest-asyncio）
- Mock 和 Fixture
- 为自己的功能编写测试

### [第 20 章：性能优化与问题排查](./chapter20.md)
- 性能瓶颈分析（async profiling）
- 内存泄漏排查
- 日志优化技巧
- 常见问题 FAQ

### [第 21 章：贡献代码与最佳实践](./chapter21.md)
- GitHub 工作流（Fork → PR）
- 代码风格（ruff 检查）
- 提交信息规范
- 代码审查清单

---

**完成后进入：**[里程碑 3 综合测试](./milestone03.md)

**恭喜完成全部学习！🎉**

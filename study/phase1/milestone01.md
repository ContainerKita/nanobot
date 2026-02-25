# 🎖️ 里程碑 1：基础检验测试

**完成第 1-7 章后进行本测试**  
**预计用时：1.5 小时**

---

## 📋 测试说明

本测试分为三部分：
1. **理论测试**（30 分钟）- 检验概念理解
2. **实践测试**（45 分钟）- 动手操作验证
3. **综合分析**（15 分钟）- 问题排查

**通过标准：**
- 理论测试：≥ 70% 正确
- 实践测试：所有任务完成
- 综合分析：能够分析出问题原因

---

## 📝 Part 1: 理论测试（30 分钟）

### 选择题（每题 5 分，共 50 分）

#### Q1: nanobot 的核心代码行数约为？
- A. 40,000 行
- B. 4,000 行
- C. 400 行
- D. 40 行

#### Q2: 配置加载的优先级从高到低是？
- A. .env → 环境变量 → 命令行参数
- B. 命令行参数 → 环境变量 → .env
- C. 环境变量 → 命令行参数 → .env
- D. 命令行参数 → .env → 环境变量

#### Q3: MessageBus 使用什么数据结构实现异步消息传递？
- A. asyncio.Queue
- B. threading.Queue
- C. list
- D. dict

#### Q4: Agent 的最大迭代次数默认是多少？
- A. 10
- B. 20
- C. 40
- D. 100

#### Q5: 以下哪个不是 Tool 必须实现的属性/方法？
- A. name
- B. description
- C. parameters
- D. priority

#### Q6: Provider 的作用是？
- A. 管理用户会话
- B. 抽象 LLM 调用接口
- C. 处理消息路由
- D. 存储对话历史

#### Q7: Channel 的 start() 方法应该是？
- A. 同步函数
- B. 异步函数（async def）
- C. 静态方法
- D. 类方法

#### Q8: InboundMessage 和 OutboundMessage 使用什么装饰器定义？
- A. @dataclass
- B. @property
- C. @staticmethod
- D. @abstractmethod

#### Q9: typer 的 @app.command() 装饰器的作用是？
- A. 创建子命令
- B. 定义配置
- C. 注册工具
- D. 启动服务

#### Q10: AgentLoop 在哪个组件上订阅消息？
- A. Provider
- B. MessageBus
- C. Channel
- D. ToolRegistry

### 简答题（每题 10 分，共 50 分）

#### Q11: 请画出一条用户消息从 Telegram 发送到 Agent 并回复的完整流程图（可以手绘拍照或文字描述）

<details>
<summary>参考答案</summary>

```
Telegram → TelegramChannel.on_message()
    ↓ 构造 InboundMessage
MessageBus.publish("inbound", msg)
    ↓
AgentLoop.subscribe("inbound")
    ↓ 处理消息
AgentLoop._run_agent_loop()
    ↓ LLM + 工具调用
MessageBus.publish("outbound", response)
    ↓
TelegramChannel.subscribe("outbound")
    ↓ 过滤 channel == "telegram"
TelegramChannel.send_message() → Telegram
```
</details>

#### Q12: 解释为什么需要最大迭代限制（max_iterations），以及什么情况会触发？

<details>
<summary>参考答案</summary>

**为什么需要：**
- 防止死循环消耗资源
- 避免无限 LLM 调用（费用）
- 保证用户体验（及时返回）

**触发情况：**
- 工具总是返回错误，Agent 反复尝试
- LLM 总是选择调用工具，无法给出最终答案
- 任务过于复杂，真的需要很多步骤
</details>

#### Q13: 列出至少 5 个 nanobot 内置的 Tool，并简述其功能

<details>
<summary>参考答案</summary>

1. **ReadFileTool**: 读取文件内容
2. **WriteFileTool**: 写入文件
3. **EditFileTool**: 编辑文件（替换内容）
4. **ListDirTool**: 列出目录内容
5. **ExecTool**: 执行 Shell 命令
6. **WebSearchTool**: Web 搜索
7. **WebFetchTool**: 获取网页内容
8. **MessageTool**: 发送消息给用户
9. **CronTool**: 管理定时任务
10. **SpawnTool**: 启动子 Agent
</details>

#### Q14: 事件驱动架构相比直接调用的优势是什么？举例说明

<details>
<summary>参考答案</summary>

**优势：**
- **解耦**: Agent 不需要知道 Channel 实现细节
- **可扩展**: 新增 Channel 无需修改 Agent 代码
- **多订阅**: 日志、监控可同时订阅消息
- **异步**: 自然支持并发处理

**示例：**
```python
# 直接调用（紧耦合）
def handle_message(msg):
    response = agent.process(msg)
    telegram.send(response)  # 🔴 Agent 依赖 Telegram

# 事件驱动（松耦合）
def agent_loop():
    async for msg in bus.subscribe("inbound"):
        response = process(msg)
        bus.publish("outbound", response)  # ✅ Agent 不知道 Channel
```
</details>

#### Q15: 如何调试一个"Agent 没有响应"的问题？列出排查步骤

<details>
<summary>参考答案</summary>

**排查步骤：**
1. **检查日志级别**: 设置 `LOG_LEVEL=DEBUG`
2. **确认消息到达**: 在 MessageBus.publish() 添加日志
3. **检查订阅者**: 确认 AgentLoop 正在订阅 "inbound"
4. **检查 Provider**: 是否 API Key 失效/网络问题
5. **检查错误捕获**: 是否有未捕获的异常
6. **检查迭代**: 是否达到 max_iterations
7. **检查过滤**: Channel 是否正确过滤 outbound 消息

**调试技巧：**
- 在关键函数添加 `logger.info()`
- 使用 `try-except` 打印完整堆栈
- 检查 `asyncio` 任务是否正常运行
</details>

---

## 🔧 Part 2: 实践测试（45 分钟）

### 任务 1：环境验证（10 分钟）

完成以下操作并截图/记录输出：

```powershell
# 1. 查看 nanobot 版本
nanobot --version

# 2. 查看帮助信息
nanobot --help

# 3. 验证配置
nanobot info  # 如果完成了第2章的练习

# 4. 检查依赖
pip show litellm pydantic typer
```

**提交：**配置信息截图

### 任务 2：基础对话测试（15 分钟）

启动 CLI 模式并完成以下对话：

```powershell
nanobot start --channel cli
```

**测试对话：**
```
1. > 你好，请介绍你自己
2. > 帮我在 workspace 创建一个 test.md 文件，内容是今天的日期
3. > 读取刚才创建的文件内容
4. > 列出 workspace 目录的所有文件
5. > 搜索"nanobot github"并总结
```

**提交：**
- 对话记录（复制粘贴）
- workspace 目录截图

### 任务 3：配置实验（10 分钟）

修改配置并观察效果：

#### 实验 1：修改模型
```bash
# 在 .env 中修改
LITELLM_MODEL=gpt-4  # 或其他可用模型
```

重新启动，对比响应质量差异。

#### 实验 2：调整迭代限制
在代码中找到 `max_iterations` 参数，改为 3，然后：

```
> 帮我搜索 Python 教程，总结成文件，然后读取确认并修改第一行为标题
```

观察是否因迭代不足而失败。

**提交：**实验结果描述

### 任务 4：日志分析（10 分钟）

设置 `LOG_LEVEL=DEBUG`，执行：

```
> 创建 hello.txt 文件，内容是 "Hello"
```

**任务：**
1. 找出日志中"调用 LLM"的记录
2. 找出"工具执行"的记录
3. 找出"发布消息"的记录
4. 统计这次对话的迭代次数

**提交：**日志片段和分析

---

## 🕵️ Part 3: 综合分析（15 分钟）

### 故障分析题

#### 场景 1：Agent 一直不回复

**日志片段：**
```
INFO | AgentLoop | Iteration 1/40
INFO | AgentLoop | Calling write_file
DEBUG | WriteFileTool | File created: workspace/test.txt
INFO | AgentLoop | Iteration 2/40
INFO | AgentLoop | Calling write_file
DEBUG | WriteFileTool | File created: workspace/test.txt
INFO | AgentLoop | Iteration 3/40
...
INFO | AgentLoop | Iteration 40/40
ERROR | AgentLoop | Maximum iterations reached
```

**问题：**Agent 为什么一直循环？可能的原因和解决方案是什么？

<details>
<summary>参考答案</summary>

**原因：**
- LLM 总是选择调用工具，没有给出最终文本回复
- 可能是 System Prompt 没有指导何时停止
- 可能是工具返回结果不清晰，LLM 无法判断任务完成

**解决方案：**
1. 优化 System Prompt，明确指导"任务完成后直接回复用户"
2. 改进工具返回消息，明确"成功/失败"
3. 调整模型（某些模型更容易陷入循环）
4. 提高 temperature 增加随机性
</details>

#### 场景 2：Telegram 消息发不出去

**日志片段：**
```
INFO | MessageBus | Publish to 'outbound': OutboundMessage(session_id='telegram_123', ...)
DEBUG | MessageBus | Delivered to 1 subscribers
```

但 Telegram 用户没有收到消息。

**问题：**可能的原因是什么？如何排查？

<details>
<summary>参考答案</summary>

**可能原因：**
1. **过滤问题**: TelegramChannel 订阅了 outbound，但过滤条件错误（`msg.channel != "telegram"`）
2. **发送失败**: Telegram API 调用失败（网络/Token 问题），但没有日志
3. **session_id 不匹配**: OutboundMessage 的 session_id 和 Telegram chat_id 不对应

**排查方法：**
1. 在 TelegramChannel 的 outbound 处理函数添加日志
2. 检查 `msg.channel == "telegram"` 条件是否生效
3. 添加 try-except 捕获发送错误
4. 打印 session_id 和 chat_id 映射关系
</details>

---

## ✅ 自我评估

完成测试后，检查：

- [ ] 理论测试得分 ≥ 70 分
- [ ] 实践任务全部完成
- [ ] 能够分析出故障场景的原因
- [ ] workspace 目录有测试文件生成
- [ ] 能够看懂 DEBUG 级别的日志

**如果全部通过，恭喜你完成第一阶段！🎉**

---

## 📚 复习建议

如果测试未通过，建议：

1. **< 60 分**: 重新学习第 1-7 章，特别关注代码阅读部分
2. **60-70 分**: 重点复习错误较多的章节
3. **实践任务卡住**: 检查环境配置，查看完整日志

---

## 🔜 下一步

通过测试后，继续[第二阶段：核心深入](../phase2/chapter08.md)

开始日期：________  
完成日期：________  
得分：______ / 100

---

**学习进度：** [███████░░░░░░░░░░░░░] 第一阶段完成！

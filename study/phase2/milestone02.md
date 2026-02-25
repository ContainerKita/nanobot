# 🎖️ 里程碑 2：核心掌握测试

**完成第 8-14 章后进行本测试**  
**预计用时：2 小时**

---

## 📋 测试说明

本测试分为三部分：

1. **理论测试**（40 分钟）- 核心机制理解
2. **实践测试**（60 分钟）- 修改与调试能力
3. **代码审查**（20 分钟）- 问题识别与改进建议

**通过标准：**

- 理论测试：≥ 70%
- 实践测试：完成 ≥ 3/4 任务
- 代码审查：关键问题识别完整

---

## 📝 Part 1: 理论测试（40 分钟）

### 选择题（每题 5 分，共 50 分）

#### Q1: `EditFileTool` 在 `old_text` 出现多次时默认行为是？
- A. 随机替换一处
- B. 替换全部
- C. 返回 warning，要求更精确上下文
- D. 直接报错退出

#### Q2: `Session.get_history()` 会从哪里开始截取消息？
- A. 从第一条消息开始
- B. 从 `last_consolidated` 之后
- C. 只保留 assistant 消息
- D. 只保留 tool 消息

#### Q3: MemoryStore 的长期记忆文件是？
- A. `sessions/*.jsonl`
- B. `memory/HISTORY.md`
- C. `memory/MEMORY.md`
- D. `cache/memory.json`

#### Q4: `CronService` 的 `cron` 模式依赖哪个库解析表达式？
- A. apscheduler
- B. croniter
- C. schedule
- D. pendulum

#### Q5: MCP 工具包装后默认命名规则是？
- A. `{tool}`
- B. `mcp_{tool}`
- C. `mcp_{server}_{tool}`
- D. `{server}_{tool}_mcp`

#### Q6: `Config` 使用的根基类是？
- A. `dataclass`
- B. `TypedDict`
- C. `BaseModel`
- D. `BaseSettings`

#### Q7: `AgentLoop` 中防止同一会话并发归档冲突的主要机制是？
- A. Queue
- B. Semaphore
- C. Lock
- D. ThreadPool

#### Q8: `ExecTool` 的超时控制主要依赖？
- A. `threading.Timer`
- B. `subprocess.run(timeout=...)`
- C. `asyncio.wait_for`
- D. `signal.alarm`

#### Q9: 当 MCP 连接失败时，`AgentLoop` 默认行为是？
- A. 直接退出进程
- B. 忽略并在后续消息时重试
- C. 永久禁用工具系统
- D. 自动回滚配置

#### Q10: `ToolsConfig` 中用于限制工具访问范围的字段是？
- A. `exec.timeout`
- B. `tools.safeMode`
- C. `restrict_to_workspace`
- D. `workspaceOnly`

### 简答题（每题 10 分，共 50 分）

#### Q11: 说明 `MEMORY.md` 与 `HISTORY.md` 的用途区别，以及它们在检索时的典型使用方式。

#### Q12: 解释为什么 `Session` 采用 append-only，而不是在归档后删除旧消息。

#### Q13: 给出 `CronService.add_job()` 到任务执行完成的完整流程。

#### Q14: 解释 `AsyncExitStack` 在 MCP 连接管理中的价值。

#### Q15: 如果用户反馈“Agent 有时不回复”，请给出你会优先检查的 5 个点。

---

## 🔧 Part 2: 实践测试（60 分钟）

### 任务 1：工具能力扩展（15 分钟）

目标：给 `ReadFileTool` 增加可选参数（如 `start_line` / `end_line`）。

要求：

- 参数定义更新到 `parameters` schema
- 参数缺省时保持原行为
- 处理越界与非法区间
- 给出清晰错误信息

### 任务 2：Cron 实战（15 分钟）

目标：创建一个“每天 09:30 的工作提醒”任务，并演示查询与手动执行。

提交：

- 创建命令
- `cron list` 输出
- 一次手动 `cron run` 结果

### 任务 3：记忆系统排查（15 分钟）

场景：用户反馈 memory 文件长期不更新。

要求：

- 给出你的排查路径（至少 4 步）
- 指出可能根因（至少 2 个）
- 给出修复建议（至少 1 条）

### 任务 4：上下文优化（15 分钟）

目标：在 `ContextBuilder` 中优化一个可读性点（例如 Runtime Context 展示内容）。

要求：

- 说明优化前后差异
- 不改变现有核心行为
- 给出验证方式

---

## 🕵️ Part 3: 代码审查（20 分钟）

阅读下面伪代码并指出问题：

```python
async def run_forever(bus, provider):
	while True:
		msg = await bus.consume_inbound()  # 无超时
		asyncio.create_task(handle(msg, provider))
```

请回答：

1. 这段代码在稳定性上有哪些隐患？（至少 3 点）
2. 在 nanobot 里对应采用了哪些改进思路？
3. 如果消息突增，你会如何做并发控制？

---

## 📤 提交建议

- 理论题答案（Markdown）
- 实践题操作记录（命令 + 关键输出）
- 代码审查分析结论

---

**完成后继续：**[第三阶段](../phase3/README.md)

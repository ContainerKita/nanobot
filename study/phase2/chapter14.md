# 第 14 章：异步编程模式与并发控制

**预计学习时间：2 小时**  
**难度：⭐⭐⭐⭐**

---

## 📚 本章目标

- 理解 nanobot 里的核心并发模式
- 掌握 `create_task` / `gather` / `wait_for` 的使用场景
- 理解 `AsyncExitStack` 在 MCP 生命周期中的价值
- 能够排查常见异步问题（卡死、超时、任务泄漏）

---

## 📖 理论学习（35 分钟）

### 1. 并发主线

典型并发结构：

- `AgentLoop.run()` 长循环消费消息
- `ChannelManager.start_all()` 并发启动多个渠道
- `CronService` 通过后台 timer task 调度

### 2. 常见异步原语

- `asyncio.create_task()`：后台任务
- `asyncio.gather()`：并发等待多个协程
- `asyncio.wait_for()`：为阻塞点加超时
- `asyncio.Lock()`：保护共享状态

### 3. 资源生命周期管理

MCP 连接使用 `AsyncExitStack` 统一管理，保障异常路径下也能释放连接资源。

---

## 💻 代码阅读（45 分钟）

### 任务 1：AgentLoop 运行循环（15 分钟）

打开：`nanobot/agent/loop.py`

重点关注：

- `run()` 中 `wait_for(..., timeout=1.0)`
- `_consolidation_tasks` 强引用集合
- `asyncio.Lock` 防止并发归档冲突

### 任务 2：CLI 交互并发（15 分钟）

打开：`nanobot/cli/commands.py` 的 `agent()` 交互模式

重点关注：

- `bus_task` 与 `outbound_task` 并行
- 退出时 `cancel + gather(return_exceptions=True)`

### 任务 3：Cron 并发调度（15 分钟）

打开：`nanobot/cron/service.py`

重点关注：

- `_arm_timer()` 的任务重建
- `_on_timer()` 顺序执行 due jobs

---

## 🔧 实践操作（40 分钟）

### 练习 1：观察超时保护（10 分钟）

让 Agent 执行一个慢命令，观察 `ExecTool` 是否按 `timeout` 返回错误。

### 练习 2：验证取消清理（15 分钟）

在交互模式中启动后中断（Ctrl+C），确认不会残留明显僵尸任务/重复输出。

### 练习 3：模拟并发归档（15 分钟）

高频发送消息触发归档，观察日志确认同一 session 不会重复并发 consolidation。

---

## ✅ 本章检查清单

- [ ] 我能区分 `gather` 与 `create_task` 的职责
- [ ] 我能解释 `wait_for` 在主循环中的意义
- [ ] 我能解释为什么归档需要 lock
- [ ] 我能给出一次异步问题的排查步骤

---

**学习进度：** [██████████████░░░░░░░] 14/21 章节  
**完成本阶段后进入：** [里程碑 2 测试](./milestone02.md)

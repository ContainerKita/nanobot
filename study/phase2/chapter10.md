# 第 10 章：记忆系统与向量检索

**预计学习时间：2 小时**  
**难度：⭐⭐⭐**

---

## 📚 本章目标

- 理解 nanobot 的“两层记忆”模型
- 掌握对话归档（consolidation）触发与执行机制
- 理解 `MEMORY.md` 与 `HISTORY.md` 的分工
- 能够调试记忆归档失败问题

---

## 📖 理论学习（35 分钟）

### 1. 两层记忆模型

`nanobot/agent/memory.py` 的核心设计：

- `MEMORY.md`：长期事实，结构化总结
- `HISTORY.md`：可 grep 的事件日志

### 2. 归档不是“删除历史”

`Session.messages` 仍保持 append-only；归档主要更新：

- `MEMORY.md`
- `HISTORY.md`
- `session.last_consolidated`

### 3. 触发机制（在 AgentLoop）

当未归档消息达到窗口阈值时，`AgentLoop` 会创建后台任务进行 consolidation，并通过锁避免并发重复归档。

---

## 💻 代码阅读（45 分钟）

### 任务 1：MemoryStore 核心逻辑（20 分钟）

打开：`nanobot/agent/memory.py`

重点关注：

- `_SAVE_MEMORY_TOOL` 的 schema
- `consolidate()` 中对旧消息切片逻辑
- 对 provider 工具调用返回值的兼容处理（string/dict）

### 任务 2：AgentLoop 的归档调度（15 分钟）

打开：`nanobot/agent/loop.py`

重点关注：

- `_consolidating` / `_consolidation_tasks`
- `_get_consolidation_lock()`
- `_consolidate_memory()`

### 任务 3：Session 侧索引位点（10 分钟）

复看：`nanobot/session/manager.py`

理解 `last_consolidated` 如何保证“归档过的消息不重复处理”。

---

## 🔧 实践操作（40 分钟）

### 练习 1：触发一次归档（20 分钟）

1. 将 `memory_window` 调小（如 8~12）
2. 连续进行多轮工具调用对话
3. 观察 `memory/MEMORY.md` 与 `memory/HISTORY.md` 是否出现新内容

### 练习 2：构造归档失败并排查（10 分钟）

模拟 provider 异常或工具未调用，观察日志：

- `LLM did not call save_memory`
- `Memory consolidation failed`

总结故障定位顺序。

### 练习 3：验证不重复归档（10 分钟）

再次触发对话，确认已归档段不会被重复写入 `HISTORY.md`。

---

## ✅ 本章检查清单

- [ ] 我能解释 `MEMORY.md` 与 `HISTORY.md` 的区别
- [ ] 我能解释 `archive_all` 与普通归档的差异
- [ ] 我能说明 `last_consolidated` 如何推进
- [ ] 我能给出归档失败的排查路径

---

**学习进度：** [██████████░░░░░░░░░░░] 10/21 章节  
**下一章：** [第 11 章：Cron 定时任务系统](./chapter11.md)

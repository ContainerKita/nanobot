# 第 11 章：Cron 定时任务系统

**预计学习时间：2 小时**  
**难度：⭐⭐⭐**

---

## 📚 本章目标

- 掌握 `CronSchedule/CronJob/CronStore` 数据结构
- 理解 `CronService` 的调度、持久化与执行流程
- 学会通过 CLI 与 Tool 两种方式管理定时任务
- 能够定位 cron 表达式与时区相关问题

---

## 📖 理论学习（35 分钟）

### 1. 三种调度模式

在 `nanobot/cron/types.py` / `nanobot/cron/service.py` 中，支持：

- `at`：一次性定时
- `every`：固定间隔
- `cron`：cron 表达式 + 可选时区

### 2. 调度核心函数

`_compute_next_run(schedule, now_ms)` 决定下一次触发时间。

- `cron` 模式依赖 `croniter`
- 支持 `zoneinfo` 时区

### 3. 运行时机制

`CronService` 使用“单计时器 + 到点扫描”：

```
start()
  → _recompute_next_runs()
  → _arm_timer()
  → _on_timer() 执行到期任务
  → 再次 _arm_timer()
```

---

## 💻 代码阅读（45 分钟）

### 任务 1：读类型定义（10 分钟）

打开：`nanobot/cron/types.py`

理解：`CronSchedule`、`CronPayload`、`CronJobState`。

### 任务 2：读服务实现（25 分钟）

打开：`nanobot/cron/service.py`

重点关注：

- `_load_store()` / `_save_store()`
- `add_job()` / `remove_job()` / `enable_job()`
- `_execute_job()` 的状态更新

### 任务 3：读接入路径（10 分钟）

打开：

- `nanobot/agent/tools/cron.py`
- `nanobot/cli/commands.py` 中 `cron_app` 子命令

---

## 🔧 实践操作（40 分钟）

### 练习 1：CLI 创建任务（15 分钟）

```powershell
nanobot cron add --name "daily-check" --cron "0 9 * * *" --tz "Asia/Shanghai" --message "请做今日工作总结"
nanobot cron list
```

验证 `jobs.json` 被写入。

### 练习 2：手动触发任务（10 分钟）

```powershell
nanobot cron run <job_id>
```

观察执行结果与 `lastStatus` 变化。

### 练习 3：在对话中使用 `cron` 工具（15 分钟）

通过 Agent 自然语言添加/删除定时任务，验证 `CronTool` 能正确转译参数并调用 `CronService`。

---

## ✅ 本章检查清单

- [ ] 我能解释 `at/every/cron` 三种模式
- [ ] 我能解释 `_arm_timer()` 为什么每次重置计时器
- [ ] 我能通过 CLI 完成 add/list/run/remove 全流程
- [ ] 我能排查 `--tz` 与 `--cron` 参数不匹配错误

---

**学习进度：** [███████████░░░░░░░░░░] 11/21 章节  
**下一章：** [第 12 章：MCP 协议与外部工具集成](./chapter12.md)

# 第 8 章：工具系统深入 - 文件/Shell 工具

**预计学习时间：2 小时**  
**难度：⭐⭐⭐**

---

## 📚 本章目标

- 理解 Tool 抽象与 `ToolRegistry` 的执行路径
- 掌握文件工具的路径约束与错误处理策略
- 掌握 Shell 工具的安全防护机制（deny/allow/workspace 限制）
- 能够定位并修改一个工具行为

---

## 📖 理论学习（35 分钟）

### 1. Tool 的统一抽象

nanobot 的每个工具都实现同一套接口：

- `name`：工具唯一标识（如 `read_file`、`exec`）
- `description`：给模型看的用途说明
- `parameters`：JSON Schema 参数定义
- `execute()`：异步执行入口

核心定义在：`nanobot/agent/tools/base.py`

### 2. 文件工具的设计重点

文件工具（`read_file` / `write_file` / `edit_file` / `list_dir`）的关键是**路径安全**和**可读报错**：

- `_resolve_path()` 将相对路径解析到 workspace
- `allowed_dir` 可限制访问范围
- `EditFileTool` 对未命中替换内容给出相似 diff 提示

### 3. Shell 工具的风险控制

`ExecTool` 通过三层防护降低误操作风险：

1. `deny_patterns`：拦截危险命令（如破坏性删除）
2. `allow_patterns`：可选白名单模式
3. `restrict_to_workspace`：限制路径逃逸与目录穿越

---

## 💻 代码阅读（45 分钟）

### 任务 1：从注册到执行（15 分钟）

1. 打开 `nanobot/agent/loop.py` 的 `_register_default_tools()`
2. 打开 `nanobot/agent/tools/registry.py` 的 `execute()`
3. 追踪一条调用链：

```
LLM 返回 tool_call
  → AgentLoop._run_agent_loop()
  → ToolRegistry.execute(name, params)
  → 对应 Tool.execute()
  → Tool result 回写到 messages
```

### 任务 2：精读文件工具（15 分钟）

打开：`nanobot/agent/tools/filesystem.py`

重点关注：

- `_resolve_path()`：相对路径 + 目录越权校验
- `EditFileTool.execute()`：
  - `old_text` 不存在时给出友好提示
  - 多次出现时返回 warning，避免误替换
- `ListDirTool.execute()`：目录/文件图标化输出

### 任务 3：精读 Shell 工具（15 分钟）

打开：`nanobot/agent/tools/shell.py`

重点关注：

- `_guard_command()` 安全检查
- `asyncio.wait_for(..., timeout=...)` 的超时控制
- 输出截断（超长结果裁剪）

---

## 🔧 实践操作（40 分钟）

### 练习 1：验证文件工具路径限制（15 分钟）

1. 在配置中启用：`tools.restrictToWorkspace = true`
2. 启动 CLI：

```powershell
nanobot agent
```

3. 分别测试：

- 读取 `./workspace` 内文件（应成功）
- 尝试读取工作区外路径（应被拒绝）

### 练习 2：验证 edit_file 的“模糊提示”行为（10 分钟）

在对话中让 Agent 执行一次不精确替换，观察是否返回：

- “old_text not found”
- best match 相似度与位置提示

### 练习 3：验证 Shell 安全策略（15 分钟）

尝试命令：

- 安全命令：`pwd` / `ls`（应通过）
- 可疑命令（应被阻断）
- 长输出命令，观察截断行为

---

## ✅ 本章检查清单

- [ ] 我能解释 Tool 抽象的四个关键属性/方法
- [ ] 我能说明 `_resolve_path()` 为什么必要
- [ ] 我能解释 `deny_patterns` 与 `allow_patterns` 的差异
- [ ] 我完成了至少 1 次文件工具和 1 次 exec 工具调试

---

**学习进度：** [████████░░░░░░░░░░░░░] 8/21 章节  
**下一章：** [第 9 章：会话管理与上下文构建](./chapter09.md)

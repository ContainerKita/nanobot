# 第 13 章：配置系统与 Pydantic 校验

**预计学习时间：2 小时**  
**难度：⭐⭐⭐**

---

## 📚 本章目标

- 理解 `Config` 与子配置模型的层级结构
- 掌握 camelCase/snake_case 双写法兼容机制
- 理解配置加载、保存与迁移流程
- 能够新增一个配置项并贯通到运行时

---

## 📖 理论学习（30 分钟）

### 1. 配置模型组织方式

`nanobot/config/schema.py` 使用分层模型：

- `channels`：各渠道参数
- `providers`：模型供应商参数
- `tools`：工具开关与限制
- `agents.defaults`：Agent 默认行为

### 2. 别名策略

`Base` 模型设置：

- `alias_generator=to_camel`
- `populate_by_name=True`

所以同一字段可接受：

- `restrict_to_workspace`
- `restrictToWorkspace`

### 3. Loader 责任

`nanobot/config/loader.py` 负责：

- `load_config()`：读取 + 校验 + 失败兜底
- `_migrate_config()`：旧字段迁移（兼容历史配置）
- `save_config()`：标准化落盘

---

## 💻 代码阅读（50 分钟）

### 任务 1：读 schema（25 分钟）

打开：`nanobot/config/schema.py`

重点关注：

- `ToolsConfig`、`MCPServerConfig`
- `AgentDefaults`（`max_tool_iterations`、`memory_window`）
- `Config.workspace_path` 属性

### 任务 2：读 loader（15 分钟）

打开：`nanobot/config/loader.py`

重点关注：

- `get_config_path()`
- `load_config()` 的容错行为
- `_migrate_config()` 的兼容逻辑

### 任务 3：读调用点（10 分钟）

打开：`nanobot/cli/commands.py`

观察 `load_config()` 结果如何传入 `AgentLoop`、`CronService`、`ChannelManager`。

---

## 🔧 实践操作（40 分钟）

### 练习 1：新增配置项（20 分钟）

目标：在 `ToolsConfig` 增加一个布尔开关（例如某个调试开关），并在某个工具/命令里读取它。

步骤：

1. 在 `schema.py` 添加字段
2. 在 `commands.py` 读取并传给组件
3. 在运行时打印/使用该开关确认生效

### 练习 2：验证 alias 兼容（10 分钟）

在配置文件中分别使用 camelCase 与 snake_case 写法，确认都可被加载。

### 练习 3：验证迁移逻辑（10 分钟）

模拟旧字段格式，确认 `_migrate_config()` 可以转换并正常启动。

---

## ✅ 本章检查清单

- [ ] 我能说明 `BaseSettings` 与 `BaseModel` 在项目中的分工
- [ ] 我能解释 alias 生成器的作用
- [ ] 我能读懂并扩展 `_migrate_config()`
- [ ] 我完成了一个“新增配置项并生效”的闭环

---

**学习进度：** [█████████████░░░░░░░░] 13/21 章节  
**下一章：** [第 14 章：异步编程模式与并发控制](./chapter14.md)

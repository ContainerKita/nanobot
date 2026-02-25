# 第 12 章：MCP 协议与外部工具集成

**预计学习时间：2 小时**  
**难度：⭐⭐⭐⭐**

---

## 📚 本章目标

- 理解 MCP 工具接入在 nanobot 中的实现方式
- 掌握 stdio 与 streamable HTTP 两种连接模式
- 理解 `MCPToolWrapper` 的包装逻辑与超时控制
- 能够完成一个 MCP Server 的最小接入

---

## 📖 理论学习（35 分钟）

### 1. MCP 在 nanobot 中的定位

MCP 让外部能力以“工具”形式接入 Agent。对上层模型而言，这些外部能力与内置工具调用体验一致。

### 2. 懒连接策略

`AgentLoop` 不是启动时强依赖 MCP，而是：

- 初始化保存配置
- 首次消息前 `_connect_mcp()`
- 连接失败记录日志，下次重试

### 3. 包装策略

`MCPToolWrapper` 将 MCP server 的工具包装成 nanobot 工具：

- 名称：`mcp_{server}_{tool}`
- 参数：直接复用 `inputSchema`
- 执行：`call_tool(...)` + 超时控制

---

## 💻 代码阅读（45 分钟）

### 任务 1：读连接器（20 分钟）

打开：`nanobot/agent/tools/mcp.py`

重点关注：

- `connect_mcp_servers()`
- stdio 模式：`StdioServerParameters` + `stdio_client`
- HTTP 模式：`streamable_http_client` + `httpx.AsyncClient(timeout=None)`

### 任务 2：读 AgentLoop 的生命周期（15 分钟）

打开：`nanobot/agent/loop.py`

重点关注：

- `_connect_mcp()`
- `close_mcp()`
- `_mcp_connected/_mcp_connecting` 状态位

### 任务 3：读配置模型（10 分钟）

打开：`nanobot/config/schema.py` 的 `MCPServerConfig` 与 `ToolsConfig.mcp_servers`。

---

## 🔧 实践操作（40 分钟）

### 练习 1：配置一个 MCP server（15 分钟）

在配置文件中添加 `tools.mcpServers` 示例项（stdio 或 HTTP 任一）。

### 练习 2：验证工具注册（10 分钟）

启动后观察日志，确认出现：

- server connected
- registered tool

### 练习 3：触发超时行为（15 分钟）

将 `toolTimeout` 设为较小值，调用慢工具，观察是否返回超时提示文本。

---

## ✅ 本章检查清单

- [ ] 我能解释 MCP 工具命名规则
- [ ] 我能解释为什么 HTTP 客户端 `timeout=None`
- [ ] 我能区分连接失败与工具超时两类问题
- [ ] 我能独立完成一个 MCP server 接入

---

**学习进度：** [████████████░░░░░░░░░] 12/21 章节  
**下一章：** [第 13 章：配置系统与 Pydantic 校验](./chapter13.md)

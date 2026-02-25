# 第 16 章：接入新的 Channel

**预计学习时间：2 小时**  
**难度：⭐⭐⭐⭐**

---

## 📚 本章目标

- 理解 Channel 在系统中的职责边界
- 掌握 `BaseChannel` 的扩展方式
- 学会实现 HTTP Webhook Channel 的最小闭环
- 能够定位常见的收发问题

---

## 📖 理论学习（35 分钟）

### 1. Channel 的核心职责

Channel 负责“平台消息”与“nanobot 内部消息”之间的双向转换：

- 入站：平台事件 → `InboundMessage`
- 出站：`OutboundMessage` → 平台发送接口

### 2. 生命周期约定

Channel 通常具备以下生命周期：

```
初始化 → start() → 监听入站 → 订阅 outbound → stop()
```

实现重点：

- `start()`/`stop()` 必须可重复调用且幂等
- 失败要有可诊断日志
- 退出时正确释放连接和后台任务

### 3. 路由与过滤原则

多渠道并行时，每个 Channel 需要根据 `channel/chat_id` 做精准过滤，避免串发。

---

## 💻 代码阅读（45 分钟）

### 任务 1：阅读基类（15 分钟）

打开：`nanobot/channels/base.py`

重点关注：

- 抽象方法定义
- 与 MessageBus 的交互方式
- 出站订阅处理结构

### 任务 2：对照现有实现（20 分钟）

任选两个渠道阅读：

- `nanobot/channels/telegram.py`
- `nanobot/channels/discord.py`
- `nanobot/channels/whatsapp.py`

重点关注：

- 平台事件如何映射为 `InboundMessage`
- 错误重试和日志策略

### 任务 3：阅读统一管理（10 分钟）

打开：`nanobot/channels/manager.py`

重点关注：

- 各渠道实例化策略
- `start_all()` / `stop_all()` 的并发处理

---

## 🔧 实践操作（40 分钟）

### 练习 1：实现 HTTP Webhook Channel（20 分钟）

目标：接收 HTTP POST 消息并发布到 bus；同时可消费 outbound 并回调到模拟接口。

最小要求：

- 支持 `channel` 与 `chat_id` 标识
- 将文本请求映射为 `InboundMessage`
- 能输出基础响应日志

### 练习 2：接入 ChannelManager（10 分钟）

要求：

- 在配置中新增启用项
- 确认 gateway 启动时能自动加载该 channel

### 练习 3：端到端验证（10 分钟）

通过本地 webhook 请求完成一次完整链路：

1. 发送入站消息
2. Agent 处理
3. Channel 收到 outbound 并输出

---

## ✅ 本章检查清单

- [ ] 我能解释 Channel 与 Agent 的解耦关系
- [ ] 我实现了一个可启动/可停止的自定义 Channel
- [ ] 我完成了至少一次端到端收发验证
- [ ] 我能定位“收到了 inbound 但无 outbound”的问题

---

**学习进度：** [████████████████░░░░░] 16/21 章节  
**下一章：** [第 17 章：添加新 Provider](./chapter17.md)

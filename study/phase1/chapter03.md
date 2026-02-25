# 第 3 章：消息总线与事件驱动架构

**预计学习时间：2 小时**  
**难度：⭐⭐**

---

## 📚 本章目标

- 理解事件驱动架构的优势
- 掌握 MessageBus 的实现原理
- 理解 InboundMessage 和 OutboundMessage 的数据结构
- 能够追踪消息在系统中的流转

---

## 📖 理论学习（30 分钟）

### 1. 什么是事件驱动架构？

**传统调用模式：**
```python
# 紧耦合
def handle_user_message(msg):
    response = agent.process(msg)
    telegram.send(response)  # 直接调用 Telegram
```

**问题：**
- ❌ Agent 必须知道所有 Channel
- ❌ 添加新 Channel 需要修改 Agent 代码
- ❌ 难以支持多 Channel 同时工作

**事件驱动模式：**
```python
# 解耦
def telegram_on_message(msg):
    bus.publish(InboundMessage(...))  # 发布到总线

def agent_loop():
    async for msg in bus.subscribe("inbound"):
        response = await process(msg)
        bus.publish(OutboundMessage(...))  # 发布响应

def telegram_sender():
    async for msg in bus.subscribe("outbound"):
        if msg.channel == "telegram":
            await telegram.send(msg)
```

**优势：**
- ✅ 组件解耦（Agent 不知道 Channel 实现）
- ✅ 易扩展（新增 Channel 无需改 Agent）
- ✅ 支持多订阅者（日志、监控可同时订阅）

### 2. nanobot 消息流程图

```
┌─────────────┐
│  Telegram   │ ──┐
│   Channel   │   │
└─────────────┘   │
                  ├─→ InboundMessage ──→ MessageBus ──→ AgentLoop
┌─────────────┐   │                          ↓
│   Discord   │ ──┘                     (处理消息)
│   Channel   │                              ↓
└─────────────┘   ┌──────────────────────────┘
                  │
                  ├─→ OutboundMessage ──→ MessageBus ──→ Channels
                  │
┌─────────────┐   │
│  Telegram   │ ←─┤
│   Channel   │   │
└─────────────┘   │
                  │
┌─────────────┐   │
│   Discord   │ ←─┘
│   Channel   │
└─────────────┘
```

### 3. 核心数据结构

#### InboundMessage（用户发给 Bot 的消息）

```python
@dataclass
class InboundMessage:
    session_id: str      # 会话 ID（区分不同用户/群组）
    content: str         # 消息文本
    channel: str         # 来源 Channel（telegram/discord）
    user_id: str         # 用户 ID
    timestamp: float     # 时间戳
    metadata: dict       # 额外元数据（图片、文件等）
```

#### OutboundMessage（Bot 发给用户的消息）

```python
@dataclass
class OutboundMessage:
    session_id: str      # 回复的会话
    content: str         # 回复文本
    channel: str         # 目标 Channel
    metadata: dict       # 元数据（格式化、图片等）
```

---

## 💻 代码阅读（50 分钟）

### 任务 1：精读 MessageBus 实现（30 分钟）

打开 [nanobot/bus/queue.py](../../nanobot/bus/queue.py)

#### 第一部分：数据结构（10 分钟）

```python
class MessageBus:
    def __init__(self):
        # 消息队列（多个订阅者共享）
        self._queues: dict[str, list[asyncio.Queue]] = {
            "inbound": [],   # 入站消息
            "outbound": [],  # 出站消息
        }
```

**理解要点：**
- `_queues` 是一个字典，键是主题（"inbound"/"outbound"）
- 值是队列列表（支持多个订阅者）
- 使用 `asyncio.Queue` 实现异步消息传递

#### 第二部分：订阅机制（10 分钟）

```python
async def subscribe(self, topic: str) -> AsyncIterator[Any]:
    """订阅某个主题的消息"""
    queue = asyncio.Queue()
    
    # 添加到订阅者列表
    if topic not in self._queues:
        self._queues[topic] = []
    self._queues[topic].append(queue)
    
    try:
        while True:
            # 等待消息
            msg = await queue.get()
            yield msg  # 返回给调用者
    finally:
        # 清理订阅
        self._queues[topic].remove(queue)
```

**关键概念：**
- `AsyncIterator` 允许用 `async for` 循环接收消息
- `queue.get()` 会阻塞直到有新消息
- `finally` 确保退出时清理订阅

**使用示例：**
```python
async for message in bus.subscribe("inbound"):
    print(f"收到消息: {message}")
```

#### 第三部分：发布机制（10 分钟）

```python
def publish(self, topic: str, message: Any) -> None:
    """发布消息到某个主题"""
    if topic not in self._queues:
        return
    
    # 发送给所有订阅者
    for queue in self._queues[topic]:
        queue.put_nowait(message)  # 非阻塞放入队列
```

**思考题：**
- Q1: 如果没有订阅者，`publish()` 会发生什么？
- Q2: `put_nowait()` 和 `await queue.put()` 有什么区别？
- Q3: 如果多个 Channel 同时订阅 "outbound"，每个都会收到所有消息吗？

### 任务 2：阅读事件定义（10 分钟）

打开 [nanobot/bus/events.py](../../nanobot/bus/events.py)

```python
@dataclass
class InboundMessage:
    """Incoming message from user"""
    session_id: str
    content: str
    channel: str
    user_id: str
    timestamp: float = field(default_factory=time.time)
    metadata: dict[str, Any] = field(default_factory=dict)

@dataclass
class OutboundMessage:
    """Outgoing message to user"""
    session_id: str
    content: str
    channel: str
    metadata: dict[str, Any] = field(default_factory=dict)
```

**学习点：**
- 使用 `@dataclass` 自动生成 `__init__`、`__repr__` 等方法
- `field(default_factory=...)` 避免可变默认值陷阱
- `metadata` 用于传递额外信息（文件、图片、格式化选项）

### 任务 3：追踪消息流转（10 分钟）

#### 发送流程（以 Telegram 为例）

**文件：**[nanobot/channels/telegram.py](../../nanobot/channels/telegram.py)

```python
class TelegramChannel(BaseChannel):
    async def _handle_message(self, update, context):
        # 1. 构造 InboundMessage
        msg = InboundMessage(
            session_id=f"telegram_{chat_id}",
            content=text,
            channel="telegram",
            user_id=str(user_id),
        )
        
        # 2. 发布到总线
        self.bus.publish("inbound", msg)
```

#### 接收流程（在 AgentLoop）

**文件：**[nanobot/agent/loop.py](../../nanobot/agent/loop.py)

```python
class AgentLoop:
    async def run(self):
        # 订阅入站消息
        async for msg in self.bus.subscribe("inbound"):
            # 处理消息
            response = await self._process(msg)
            
            # 发布出站消息
            self.bus.publish("outbound", OutboundMessage(
                session_id=msg.session_id,
                content=response,
                channel=msg.channel,
            ))
```

#### 回复流程（回到 Telegram）

```python
class TelegramChannel(BaseChannel):
    async def _send_loop(self):
        # 订阅出站消息
        async for msg in self.bus.subscribe("outbound"):
            if msg.channel == "telegram":
                # 发送到 Telegram
                await self._send_message(msg)
```

---

## 🔧 实践操作（40 分钟）

### 练习 1：消息流追踪（15 分钟）

**目标：**添加日志，追踪一条消息的完整流程。

#### 步骤 1：修改 MessageBus

在 `bus/queue.py` 的 `publish()` 方法添加日志：

```python
def publish(self, topic: str, message: Any) -> None:
    logger.debug(f"[MessageBus] Publish to '{topic}': {message}")
    
    if topic not in self._queues:
        logger.warning(f"[MessageBus] No subscribers for '{topic}'")
        return
    
    for queue in self._queues[topic]:
        queue.put_nowait(message)
    
    logger.debug(f"[MessageBus] Delivered to {len(self._queues[topic])} subscribers")
```

#### 步骤 2：运行并观察

```powershell
# 确保 LOG_LEVEL=DEBUG
nanobot start --channel cli

# 发送消息
> hello
```

**预期日志片段：**
```
DEBUG | MessageBus | Publish to 'inbound': InboundMessage(session_id='cli_...')
DEBUG | MessageBus | Delivered to 1 subscribers
...
DEBUG | MessageBus | Publish to 'outbound': OutboundMessage(...)
DEBUG | MessageBus | Delivered to 1 subscribers
```

### 练习 2：自定义消息类型（20 分钟）

**目标：**添加一个 "系统通知" 消息类型。

#### 步骤 1：定义新事件

在 `bus/events.py` 添加：

```python
@dataclass
class SystemNotification:
    """System notification event"""
    level: str  # "info" | "warning" | "error"
    message: str
    timestamp: float = field(default_factory=time.time)
```

#### 步骤 2：发布通知

在 `agent/loop.py` 的 `run()` 方法添加：

```python
async def run(self):
    # 启动时发送通知
    from nanobot.bus.events import SystemNotification
    self.bus.publish("system", SystemNotification(
        level="info",
        message="AgentLoop started successfully"
    ))
    
    # 原有逻辑...
```

#### 步骤 3：订阅并打印

在 `cli/commands.py` 的 `_run_channels()` 函数添加：

```python
async def _run_channels(channels, loop):
    # 添加系统通知监听
    async def log_system_notifications():
        from nanobot.bus.events import SystemNotification
        async for notif in loop.bus.subscribe("system"):
            if isinstance(notif, SystemNotification):
                print(f"[{notif.level.upper()}] {notif.message}")
    
    async with AsyncExitStack() as stack:
        await stack.enter_async_context(loop)
        
        # 并发运行
        await asyncio.gather(
            *[channel.start() for channel in channels],
            log_system_notifications(),  # 新增
        )
```

#### 步骤 4：测试

```powershell
nanobot start --channel cli
# 应该看到：[INFO] AgentLoop started successfully
```

### 练习 3：消息过滤器（5 分钟）

**思考：**如何让 Telegram Channel 只接收自己的消息？

当前代码：
```python
async for msg in self.bus.subscribe("outbound"):
    if msg.channel == "telegram":  # 过滤
        await self._send(msg)
```

**思考题：**
- Q: 如果有 10 个 Channel，每个都订阅 "outbound" 并过滤，效率如何？
- Q: 能否为每个 Channel 创建专用主题（如 "outbound_telegram"）？
- Q: 哪种方案更好？为什么？

---

## ✅ 本章练习答案

### 思考题答案

**Q1: 没有订阅者会发生什么？**
消息会被丢弃（`publish()` 直接 return）。这是合理的，因为没人需要这条消息。

**Q2: put_nowait() 和 await queue.put() 的区别**
- `put_nowait()`: 立即放入，队列满时抛异常
- `await queue.put()`: 队列满时等待直到有空间

**Q3: 多订阅者是否都收到消息？**
是的！每个订阅者都有独立的队列，都会收到副本。

**消息过滤效率：**
10 个 Channel 都过滤效率低，但实现简单。专用主题效率高，但增加复杂度。nanobot 选择简单方案，因为 Channel 数量有限。

---

## 📝 本章总结

完成本章后，你应该能够：

- ✅ 理解事件驱动架构的优势
- ✅ 掌握 `asyncio.Queue` 的基本用法
- ✅ 能追踪消息在 Bus → Agent → Channel 的流转
- ✅ 理解 `async for` 和 `AsyncIterator` 的关系
- ✅ 能够添加自定义消息类型

**知识点检查清单：**
- [ ] 能画出消息流转图
- [ ] 能解释 `subscribe()` 为什么使用 `yield`
- [ ] 理解 `@dataclass` 的作用
- [ ] 知道如何调试消息传递问题（添加日志）

---

## 🔜 下一章预告

[第 4 章：Agent 核心循环机制](./chapter04.md)

我们将深入 Agent 的"大脑"：
- LLM 调用的完整流程
- 工具选择与执行
- 多轮对话机制
- 最大迭代控制

---

**学习进度：** [███░░░░░░░░░░░░░░░░░] 3/21 章节完成

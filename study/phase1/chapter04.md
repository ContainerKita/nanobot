# 第 4 章：Agent 核心循环机制

**预计学习时间：2 小时**  
**难度：⭐⭐⭐**

---

## 📚 本章目标

- 深入理解 Agent 的"思考-执行-观察"循环
- 掌握 LLM 调用和工具执行的完整流程
- 理解最大迭代限制的必要性
- 能够调试 Agent 卡住或死循环的问题

---

## 📖 理论学习（35 分钟）

### 1. ReAct 模式简介

nanobot 使用 **ReAct** (Reasoning + Acting) 模式：

```
循环开始
  ↓
思考（Reasoning）: LLM 决定下一步做什么
  ↓
行动（Acting）: 执行工具调用
  ↓
观察（Observing）: 获取工具执行结果
  ↓
[重复直到 LLM 返回最终答案或达到最大迭代]
  ↓
循环结束
```

**示例对话：**
```
User: 帮我创建一个 hello.txt 文件，内容是 "Hello World"

Iteration 1:
  LLM 思考: 需要使用 write_file 工具
  执行工具: write_file(path="hello.txt", content="Hello World")
  工具返回: "File created successfully"

Iteration 2:
  LLM 思考: 任务完成，可以回复用户了
  返回: "已创建 hello.txt 文件，内容为 'Hello World'"
```

### 2. 核心循环流程

```python
async def _process_message(msg):
    conversation = [
        {"role": "system", "content": system_prompt},
        {"role": "user", "content": msg.content}
    ]
    
    for iteration in range(max_iterations):
        # 1. 调用 LLM
        response = await provider.chat(conversation, tools=available_tools)
        
        # 2. 检查是否有工具调用
        if response.has_tool_calls:
            # 3. 执行所有工具
            for tool_call in response.tool_calls:
                result = await execute_tool(tool_call)
                conversation.append({
                    "role": "tool",
                    "content": result,
                    "tool_call_id": tool_call.id
                })
            # 继续循环
        else:
            # 4. LLM 返回最终答案
            return response.content
    
    # 达到最大迭代
    return "抱歉，处理超时..."
```

### 3. 关键概念

- **Conversation History**: 对话历史，包含用户消息、助手回复、工具调用结果
- **Tool Schema**: 工具的 JSON Schema 描述，告诉 LLM 有哪些工具可用
- **Max Iterations**: 防止死循环，默认 40 次
- **Streaming**: 流式返回，边生成边显示

---

## 💻 代码阅读（50 分钟）

### 任务 1：精读 AgentLoop 核心逻辑（35 分钟）

打开 [nanobot/agent/loop.py](../../nanobot/agent/loop.py)

#### 第一部分：初始化（10 分钟）

找到 `__init__` 方法，理解组件依赖：

```python
class AgentLoop:
    def __init__(
        self,
        bus: MessageBus,           # 消息总线
        provider: LLMProvider,     # LLM 提供者
        workspace: Path,           # 工作目录
        model: str | None = None,
        max_iterations: int = 40,  # 最大循环次数
        ...
    ):
        self.bus = bus
        self.provider = provider
        self.workspace = workspace
        
        # 核心组件
        self.context = ContextBuilder(workspace)  # 构建上下文
        self.memory = MemoryStore(workspace)       # 记忆存储
        self.tools = ToolRegistry()                # 工具注册表
        self.sessions = SessionManager(workspace)  # 会话管理
        
        # 注册内置工具
        self._register_tools()
```

**阅读任务：**找到 `_register_tools()` 方法，看注册了哪些工具。

#### 第二部分：主循环（15 分钟）

找到 `run()` 方法：

```python
async def run(self):
    """Agent 主循环"""
    try:
        async for message in self.bus.subscribe("inbound"):
            # 处理每条消息
            await self._handle_message(message)
    except Exception as e:
        logger.error(f"AgentLoop error: {e}")
```

然后追踪 `_handle_message()` → `_process_turn()` → `_run_agent_loop()`

#### 第三部分：Agent 迭代逻辑（10 分钟）

找到 `_run_agent_loop()` 方法（核心中的核心）：

```python
async def _run_agent_loop(self, session, user_message):
    conversation = self._build_conversation(session, user_message)
    
    for i in range(self.max_iterations):
        logger.debug(f"Iteration {i+1}/{self.max_iterations}")
        
        # 调用 LLM
        response = await self.provider.chat(
            messages=conversation,
            tools=self.tools.to_schema(),  # 工具列表
            model=self.model,
            temperature=self.temperature,
        )
        
        # 处理工具调用
        if response.has_tool_calls:
            for tc in response.tool_calls:
                result = await self._execute_tool(tc)
                # 添加到对话历史
                conversation.append(...)
        else:
            # 返回最终答案
            return response.content
    
    return "Maximum iterations reached"
```

**关键点：**
- 每次迭代都把工具结果加入 conversation
- LLM 看到历史，知道上次工具执行结果
- `has_tool_calls` 决定是继续循环还是结束

### 任务 2：工具执行流程（15 分钟）

找到 `_execute_tool()` 方法：

```python
async def _execute_tool(self, tool_call):
    tool = self.tools.get(tool_call.name)
    
    try:
        # 执行工具
        result = await tool.execute(**tool_call.arguments)
        return result
    except Exception as e:
        return f"Tool error: {e}"
```

**思考题：**
- Q1: 如果工具执行出错，Agent 会怎样？
- Q2: LLM 看到错误信息后会做什么？

---

## 🔧 实践操作（35 分钟）

### 练习 1：添加迭代日志（15 分钟）

修改 `_run_agent_loop()` 添加详细日志：

```python
for i in range(self.max_iterations):
    logger.info(f"🔄 Iteration {i+1}/{self.max_iterations}")
    
    response = await self.provider.chat(...)
    
    if response.has_tool_calls:
        logger.info(f"🛠️  Calling {len(response.tool_calls)} tool(s)")
        for tc in response.tool_calls:
            logger.info(f"   → {tc.name}({tc.arguments})")
            result = await self._execute_tool(tc)
            logger.debug(f"   ← Result: {result[:100]}...")  # 截断长结果
    else:
        logger.info(f"✅ Final answer ready")
        return response.content
```

测试：
```powershell
nanobot start --channel cli
> 帮我创建 test1.txt, test2.txt, test3.txt 三个文件
```

观察日志中的迭代过程。

### 练习 2：调整最大迭代（10 分钟）

在 `.env` 添加：
```bash
MAX_ITERATIONS=5  # 降低到 5 次
```

（需要修改 `cli/commands.py` 支持这个配置）

尝试复杂任务，观察是否会超时：
```
> 帮我搜索 nanobot 相关资料，总结成 summary.md，然后读取文件内容确认
```

### 练习 3：死循环调试（10 分钟）

**制造问题：**修改某个工具让它总是返回错误

```python
# 在 tools/filesystem.py 的 WriteFileTool.execute() 添加
async def execute(self, path: str, content: str):
    raise Exception("Simulated error")  # 故意报错
```

运行：
```
> 创建一个文件
```

**观察：**
- Agent 会尝试多次吗？
- 达到 max_iterations 后如何处理？
- 日志中如何体现决策过程？

---

## 📝 本章总结

完成本章后，你应该能够：

- ✅ 理解 ReAct 模式的思考-行动-观察循环
- ✅ 能够追踪一次完整的 Agent 处理流程
- ✅ 理解工具调用如何影响下一轮 LLM 决策
- ✅ 能够调试 Agent 循环问题（日志、迭代限制）

**知识点检查清单：**
- [ ] 能画出 Agent 循环流程图
- [ ] 理解为什么需要最大迭代限制
- [ ] 知道 conversation history 的结构
- [ ] 能识别死循环的原因（工具总返回错误等）

---

**学习进度：** [████░░░░░░░░░░░░░░░░] 4/21 章节完成

# 快速参考指南

> 学习过程中的速查手册

---

## 常用命令

### 项目管理
```powershell
# 激活虚拟环境
.\venv\Scripts\Activate.ps1

# 安装/更新依赖
pip install -e .
pip install -e ".[dev]"

# 运行测试
pytest
pytest tests/test_xxx.py -v

# 代码检查
ruff check .
ruff check --fix .
```

### nanobot 命令
```powershell
# 启动 CLI 模式
nanobot start --channel cli

# 启动 Telegram
nanobot start --channel telegram

# 管理 Cron 任务
nanobot cron list
nanobot cron add "0 9 * * *" "每日总结"

# 查看帮助
nanobot --help
nanobot start --help
```

---

## 核心文件速查

### 入口和配置
| 文件 | 作用 |
|------|------|
| `nanobot/__main__.py` | 程序入口 |
| `nanobot/cli/commands.py` | CLI 命令定义 |
| `nanobot/config/schema.py` | 配置数据结构 |
| `nanobot/config/loader.py` | 配置加载逻辑 |
| `.env` | 环境变量配置 |

### 核心组件
| 文件 | 作用 |
|------|------|
| `nanobot/agent/loop.py` | Agent 主循环 |
| `nanobot/bus/queue.py` | 消息总线 |
| `nanobot/bus/events.py` | 事件定义 |
| `nanobot/session/manager.py` | 会话管理 |
| `nanobot/agent/context.py` | 上下文构建 |
| `nanobot/agent/memory.py` | 记忆系统 |

### 工具系统
| 文件 | 作用 |
|------|------|
| `nanobot/agent/tools/base.py` | Tool 基类 |
| `nanobot/agent/tools/registry.py` | 工具注册表 |
| `nanobot/agent/tools/filesystem.py` | 文件操作工具 |
| `nanobot/agent/tools/shell.py` | Shell 执行工具 |
| `nanobot/agent/tools/web.py` | Web 搜索/抓取 |
| `nanobot/agent/tools/cron.py` | 定时任务工具 |

### Provider 和 Channel
| 文件 | 作用 |
|------|------|
| `nanobot/providers/base.py` | Provider 基类 |
| `nanobot/providers/litellm_provider.py` | LiteLLM 实现 |
| `nanobot/channels/base.py` | Channel 基类 |
| `nanobot/channels/telegram.py` | Telegram 适配器 |
| `nanobot/channels/cli.py` | CLI 适配器 |

---

## 数据结构速查

### 消息类型
```python
# 入站消息（用户 → Bot）
InboundMessage(
    session_id="telegram_123456",
    content="帮我创建文件",
    channel="telegram",
    user_id="123456",
    timestamp=1234567890.0,
    metadata={}
)

# 出站消息（Bot → 用户）
OutboundMessage(
    session_id="telegram_123456",
    content="文件已创建",
    channel="telegram",
    metadata={}
)
```

### LLM 相关
```python
# 工具调用请求
ToolCallRequest(
    id="call_abc123",
    name="write_file",
    arguments={"path": "test.txt", "content": "hello"}
)

# LLM 响应
LLMResponse(
    content="我来帮你创建文件",
    tool_calls=[ToolCallRequest(...)],
    finish_reason="tool_calls",
    usage={"prompt_tokens": 100, "completion_tokens": 20}
)
```

### 配置结构
```python
BotConfig(
    provider="litellm",
    workspace_dir=Path("./workspace"),
    litellm_api_key="sk-xxx",
    litellm_model="gpt-4o-mini",
    channels=ChannelsConfig(
        enabled=["cli"],
        telegram=TelegramConfig(token="xxx")
    )
)
```

---

## 异步编程速查

### 基本模式
```python
# 定义协程
async def my_function():
    await asyncio.sleep(1)
    return "done"

# 运行协程
asyncio.run(my_function())

# 并发运行
await asyncio.gather(
    task1(),
    task2(),
    task3()
)

# 异步迭代
async for item in async_generator():
    process(item)
```

### 常用工具
```python
# 创建队列
queue = asyncio.Queue()
await queue.put(item)
item = await queue.get()

# 超时控制
try:
    await asyncio.wait_for(task(), timeout=5)
except asyncio.TimeoutError:
    print("超时")

# 资源管理
async with AsyncExitStack() as stack:
    resource = await stack.enter_async_context(manager)
```

---

## Pydantic 速查

### 基本用法
```python
from pydantic import BaseModel, Field

class User(BaseModel):
    name: str = Field(..., min_length=1)  # 必填
    age: int = Field(default=0, ge=0, le=150)  # 可选，范围限制
    email: str | None = None  # 可选
    
user = User(name="Alice", age=25)
```

### 环境变量配置
```python
from pydantic_settings import BaseSettings

class Config(BaseSettings):
    api_key: str = Field(alias="MY_API_KEY")
    
    class Config:
        env_file = ".env"
        
# 自动从环境变量 MY_API_KEY 读取
config = Config()
```

---

## 调试技巧速查

### 添加日志
```python
from loguru import logger

# 不同级别
logger.debug("详细信息")
logger.info("一般信息")
logger.warning("警告")
logger.error("错误")

# 格式化
logger.info(f"User {user_id} sent: {message}")
```

### 设置日志级别
```bash
# .env 文件
LOG_LEVEL=DEBUG  # DEBUG | INFO | WARNING | ERROR
```

### 调试异步代码
```python
# 打印当前任务
import asyncio
print(asyncio.current_task())

# 打印所有任务
print(asyncio.all_tasks())

# 捕获异常
try:
    await some_async_function()
except Exception as e:
    logger.exception("Caught exception:")  # 自动打印堆栈
```

---

## 测试速查

### pytest 基础
```python
# 测试文件：tests/test_xxx.py
def test_something():
    assert 1 + 1 == 2

# 运行
pytest tests/test_xxx.py
pytest -v  # 详细输出
pytest -k "test_name"  # 运行特定测试
```

### 异步测试
```python
import pytest

@pytest.mark.asyncio
async def test_async_function():
    result = await my_async_function()
    assert result == expected
```

### Mock
```python
from unittest.mock import Mock, AsyncMock, patch

# Mock 对象
mock_provider = Mock()
mock_provider.chat.return_value = LLMResponse(content="test")

# Mock 异步
mock_tool = AsyncMock()
mock_tool.execute.return_value = "success"

# Patch
with patch('nanobot.agent.loop.Provider') as mock:
    mock.return_value.chat.return_value = ...
```

---

## Git 工作流速查

```powershell
# 状态查看
git status
git log --oneline

# 分支管理
git checkout -b feature/my-feature
git branch -a

# 提交代码
git add .
git commit -m "feat: add new tool"
git push origin feature/my-feature

# 同步主分支
git checkout main
git pull origin main
git checkout feature/my-feature
git merge main
```

---

## 常见错误码

| 错误信息 | 可能原因 | 解决方案 |
|----------|----------|----------|
| `ModuleNotFoundError: No module named 'nanobot'` | 未安装或虚拟环境未激活 | `pip install -e .` |
| `OpenAI API key not found` | API Key 未配置 | 检查 `.env` 文件 |
| `asyncio.run() cannot be called from a running event loop` | 嵌套调用 asyncio.run | 改用 `await` |
| `Maximum iterations reached` | Agent 循环超限 | 简化任务或调整 max_iterations |
| `ValidationError` | Pydantic 校验失败 | 检查配置类型和约束 |

---

**打印此页随时查阅！**

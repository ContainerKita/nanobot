# 第 2 章：入口流程与 CLI 命令系统

**预计学习时间：2 小时**  
**难度：⭐⭐**

---

## 📚 本章目标

- 深入理解 nanobot 的启动流程
- 掌握 typer 库的基本用法
- 理解配置加载机制（pydantic-settings）
- 能够添加自定义 CLI 命令

---

## 📖 理论学习（35 分钟）

### 1. typer 库简介

**typer** 是一个构建 CLI 应用的现代 Python 库，基于类型提示自动生成命令行接口。

#### 核心概念

```python
import typer

app = typer.Typer()  # 创建 CLI 应用

@app.command()  # 装饰器把函数变成命令
def hello(name: str):  # 参数类型会自动转换
    """Say hello to NAME."""
    print(f"Hello {name}!")

# 使用：python script.py hello Alice
```

**自动功能：**
- ✅ 参数解析（--name, -n）
- ✅ 类型转换和验证
- ✅ 帮助文档生成（--help）
- ✅ 子命令支持

#### 在 nanobot 中的应用

```python
# nanobot/cli/commands.py
app = typer.Typer(name="nanobot", help="...")

@app.command()
def start(...): ...  # nanobot start

@app.command()  
def cron(...): ...   # nanobot cron
```

### 2. 配置加载机制

nanobot 使用 **pydantic-settings** 实现类型安全的配置管理。

#### 配置优先级（从高到低）

```
命令行参数 > 环境变量 > .env 文件 > 默认值
```

#### 示例流程

```python
# 1. 定义配置 Schema
class BotConfig(BaseSettings):
    api_key: str = Field(default="")
    model: str = Field(default="gpt-4")
    
    class Config:
        env_prefix = "LITELLM_"  # 环境变量前缀
        env_file = ".env"

# 2. 加载配置
config = BotConfig()  # 自动读取 LITELLM_API_KEY, LITELLM_MODEL

# 3. 使用配置
provider = LiteLLMProvider(api_key=config.api_key)
```

### 3. nanobot 启动流程详解

```
用户执行: nanobot start
          ↓
__main__.py → cli.commands.app()
          ↓
typer 解析命令 → start() 函数
          ↓
ConfigLoader.from_env() → 加载配置
          ↓
初始化组件（Provider, Bus, Loop, Channels）
          ↓
asyncio.run(main_async())  → 启动异步事件循环
          ↓
各 Channel 并发运行（await asyncio.gather）
```

---

## 💻 代码阅读（45 分钟）

### 任务 1：精读 CLI 命令入口（30 分钟）

打开 [nanobot/cli/commands.py](../../nanobot/cli/commands.py)

#### 第一部分：命令定义（10 分钟）

```python
# 找到这段代码并理解
app = typer.Typer(
    name="nanobot",
    help="Ultra-lightweight personal AI assistant",
    add_completion=False,
)

@app.command()
def start(
    # 参数 1: 配置文件路径
    config_path: Annotated[
        str | None,
        typer.Option("--config", "-c", help="Path to config file")
    ] = None,
    
    # 参数 2: Channel 选择
    channel: Annotated[
        str | None,
        typer.Option("--channel", help="Channel to use")
    ] = None,
    
    # ... 其他参数
):
    """Start the nanobot agent."""
    ...
```

**阅读要点：**
- ❓ `Annotated` 类型提示的作用是什么？
- ❓ `typer.Option` 如何指定短参数（-c）和长参数（--config）？
- ❓ 如果用户不传 `--channel`，默认值从哪里来？

#### 第二部分：配置加载（10 分钟）

```python
# 在 start() 函数内找到配置加载逻辑
def start(...):
    # 步骤 1: 加载配置
    if config_path:
        config = ConfigLoader.from_file(config_path)
    else:
        config = ConfigLoader.from_env()  # 从环境变量/. env
    
    # 步骤 2: 命令行参数覆盖配置
    if channel:
        config.channels.enabled = [channel]
```

**跳转阅读：**[nanobot/config/loader.py](../../nanobot/config/loader.py)

```python
class ConfigLoader:
    @staticmethod
    def from_env() -> BotConfig:
        """从环境变量加载配置"""
        return BotConfig()  # pydantic-settings 自动处理
```

**思考题：**
- Q1: 如果 `.env` 和命令行都设置了 `channel`，哪个生效？
- Q2: `BotConfig()` 在构造时做了什么？（提示：看 pydantic BaseSettings）

#### 第三部分：异步启动流程（10 分钟）

```python
def start(...):
    # ... 配置加载 ...
    
    # 步骤 3: 初始化核心组件
    bus = MessageBus()
    provider = _create_provider(config)
    loop = AgentLoop(bus, provider, ...)
    
    # 步骤 4: 初始化 Channels
    channels = []
    for channel_name in config.channels.enabled:
        channel = _create_channel(channel_name, config, bus)
        channels.append(channel)
    
    # 步骤 5: 启动异步循环
    asyncio.run(_run_channels(channels, loop))
```

找到 `_run_channels()` 函数：

```python
async def _run_channels(channels, loop):
    async with AsyncExitStack() as stack:
        # 启动 AgentLoop
        await stack.enter_async_context(loop)
        
        # 并发启动所有 Channels
        await asyncio.gather(
            *[channel.start() for channel in channels]
        )
```

**理解要点：**
- `AsyncExitStack` 确保正确清理资源
- `asyncio.gather` 并发运行多个协程
- 所有 Channel 同时监听消息

### 任务 2：阅读配置定义（15 分钟）

打开 [nanobot/config/schema.py](../../nanobot/config/schema.py)

#### 关键数据结构

```python
class ChannelsConfig(BaseModel):
    """Channel 配置"""
    enabled: list[str] = Field(default=["cli"])
    telegram: TelegramConfig | None = None
    discord: DiscordConfig | None = None
    # ...

class BotConfig(BaseSettings):
    """总配置"""
    provider: str = Field(default="litellm")
    workspace_dir: Path = Field(default=Path("./workspace"))
    channels: ChannelsConfig = Field(default_factory=ChannelsConfig)
    
    # LiteLLM 配置
    litellm_api_key: str = Field(default="", alias="LITELLM_API_KEY")
    litellm_model: str = Field(default="gpt-4", alias="LITELLM_MODEL")
    
    class Config:
        env_file = ".env"
        env_file_encoding = "utf-8"
```

**学习重点：**
- `Field` 的 `alias` 参数：环境变量映射
- `BaseSettings` vs `BaseModel` 的区别
- 嵌套配置结构（`channels.telegram.token`）

---

## 🔧 实践操作（40 分钟）

### 练习 1：添加自定义命令（20 分钟）

**目标：**添加一个 `nanobot info` 命令，显示系统信息。

#### 步骤 1：编辑 `cli/commands.py`

在文件末尾添加：

```python
@app.command()
def info():
    """Display nanobot system information."""
    import sys
    from pathlib import Path
    
    print("🤖 nanobot System Information")
    print("=" * 40)
    print(f"Python Version: {sys.version}")
    print(f"Workspace: {Path.cwd()}")
    
    # 尝试加载配置
    try:
        config = ConfigLoader.from_env()
        print(f"Provider: {config.provider}")
        print(f"Model: {config.litellm_model}")
        print(f"Channels: {', '.join(config.channels.enabled)}")
    except Exception as e:
        print(f"Config Error: {e}")
```

#### 步骤 2：测试命令

```powershell
nanobot info
```

**预期输出：**
```
🤖 nanobot System Information
========================================
Python Version: 3.11.x
Workspace: e:\hobby\momo\nanobot
Provider: litellm
Model: gpt-4o-mini
Channels: cli
```

### 练习 2：配置实验（15 分钟）

#### 实验 1：环境变量优先级

```powershell
# 在 .env 中设置
LITELLM_MODEL=gpt-4

# 临时环境变量覆盖
$env:LITELLM_MODEL="gpt-4o-mini"
nanobot info

# 观察：哪个模型被使用？
```

#### 实验 2：配置文件

创建 `config.custom.env`：

```bash
PROVIDER=litellm
LITELLM_MODEL=deepseek-chat
LITELLM_BASE_URL=https://api.deepseek.com
CHANNELS=cli
```

使用自定义配置：

```powershell
# 注意：当前版本可能不直接支持 --config，这是学习练习
# 你可以修改代码支持它
nanobot start --config config.custom.env
```

### 练习 3：日志级别控制（5 分钟）

修改 `.env` 添加：

```bash
LOG_LEVEL=DEBUG
```

重新启动，观察日志详细程度变化：

```powershell
nanobot start --channel cli
```

**对比观察：**
- INFO 级别：只显示关键步骤
- DEBUG 级别：显示所有函数调用、参数传递

---

## ✅ 本章练习答案

### 思考题答案

**Q1: 配置优先级**
命令行参数 > 环境变量 > .env 文件。所以命令行的 `--channel` 优先级最高。

**Q2: BotConfig() 构造时做了什么？**
pydantic-settings 的 `BaseSettings` 会：
1. 读取 `.env` 文件
2. 读取环境变量
3. 应用默认值
4. 进行类型验证

**实验 1 答案：**
环境变量 `$env:LITELLM_MODEL` 会覆盖 `.env` 文件中的设置。

---

## 📝 本章总结

完成本章后，你应该能够：

- ✅ 理解 typer 的基本用法和装饰器机制
- ✅ 追踪 `nanobot start` 的完整启动流程
- ✅ 理解配置加载的优先级规则
- ✅ 能够添加自定义 CLI 命令
- ✅ 知道如何调整日志级别进行调试

**知识点检查清单：**
- [ ] 能画出 nanobot 启动流程图
- [ ] 能解释 `@app.command()` 的作用
- [ ] 知道配置的三个来源和优先级
- [ ] 理解 `asyncio.run()` 和 `asyncio.gather()` 的区别
- [ ] 能够修改配置 Schema 添加新字段

---

## 🔜 下一章预告

[第 3 章：消息总线与事件驱动架构](./chapter03.md)

我们将学习：
- MessageBus 的实现原理
- 事件驱动架构的优势
- InboundMessage 和 OutboundMessage 的流转
- 如何调试消息传递问题

---

**学习进度：** [██░░░░░░░░░░░░░░░░░░] 2/21 章节完成

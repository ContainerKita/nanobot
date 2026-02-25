# 第 1 章：项目概览与环境搭建

**预计学习时间：2 小时**  
**难度：⭐**

---

## 📚 本章目标

- 理解 nanobot 项目的定位和价值
- 搭建本地开发环境
- 成功运行项目并验证基本功能
- 理解项目目录结构和核心模块职责

---

## 📖 理论学习（30 分钟）

### 1. 什么是 nanobot？

nanobot 是一个**超轻量级个人 AI 助手框架**，核心代码仅 ~4000 行，但麻雀虽小五脏俱全：

**核心特性：**
- ✅ **Agent 框架**：完整的 LLM Agent 循环（思考 → 调用工具 → 观察 → 循环）
- ✅ **多渠道接入**：Telegram、Discord、Slack、飞书、钉钉、企业微信等
- ✅ **工具系统**：文件操作、Shell 执行、Web 搜索、定时任务等
- ✅ **记忆系统**：长短期记忆、向量检索
- ✅ **可扩展**：Provider（LLM）、Channel（通讯渠道）、Tool（工具）都可自定义

**与 OpenClaw、Clawdbot 的区别：**
| 特性 | nanobot | Clawdbot |
|------|---------|----------|
| 代码量 | ~4000 行 | 430,000+ 行 |
| 定位 | 教育 + 研究 + 实用 | 生产级复杂系统 |
| 学习曲线 | 平缓 | 陡峭 |
| 适合人群 | 学习者、研究者 | 企业级应用 |

### 2. 项目架构鸟瞰

```
用户消息（Telegram/Discord/CLI）
    ↓
Channel（渠道层，适配不同平台）
    ↓
MessageBus（消息总线，事件驱动）
    ↓
AgentLoop（Agent 核心循环）
    ├─ ContextBuilder（构建上下文）
    ├─ MemoryStore（记忆检索）
    ├─ Provider（调用 LLM）
    └─ ToolRegistry（执行工具）
    ↓
Response 回流到 Channel
```

### 3. 技术栈速览

- **语言**：Python 3.11+（使用了类型提示、match-case 等新特性）
- **异步框架**：asyncio（所有 I/O 都是异步的）
- **LLM 调用**：litellm（统一多家 LLM API）
- **配置管理**：pydantic（类型安全的配置）
- **CLI 工具**：typer（命令行接口）
- **日志**：loguru（优雅的日志库）

---

## 💻 代码阅读（40 分钟）

### 任务 1：理解项目结构（15 分钟）

打开项目根目录，观察文件组织：

```
nanobot/
├── nanobot/              # 主代码目录
│   ├── __main__.py       # 【关键】入口文件
│   ├── agent/            # 【核心】Agent 相关代码
│   │   ├── loop.py       # → Agent 主循环
│   │   ├── context.py    # → 上下文构建
│   │   ├── memory.py     # → 记忆系统
│   │   └── tools/        # → 工具集
│   ├── channels/         # 【扩展】多渠道支持
│   │   ├── base.py       # → Channel 基类
│   │   ├── telegram.py   # → Telegram 适配器
│   │   └── ...
│   ├── providers/        # 【扩展】LLM Provider
│   │   ├── base.py       # → Provider 基类
│   │   └── litellm_provider.py  # → 通用实现
│   ├── bus/              # 消息总线
│   ├── config/           # 配置系统
│   └── cli/              # CLI 命令
├── tests/                # 测试用例
├── pyproject.toml        # 项目依赖和元数据
└── README.md
```

**阅读顺序建议：**
1. [pyproject.toml](../pyproject.toml) - 了解依赖
2. [nanobot/__main__.py](../nanobot/__main__.py) - 找到入口
3. [nanobot/cli/commands.py](../nanobot/cli/commands.py) - 看 CLI 如何启动

### 任务 2：阅读入口代码（25 分钟）

#### 文件 1: `nanobot/__main__.py`（5 分钟）

```python
from nanobot.cli.commands import app

if __name__ == "__main__":
    app()
```

> 📝 **笔记提示**：这里直接调用了 CLI 的 typer app，说明所有功能都通过命令行入口启动。

#### 文件 2: `nanobot/cli/commands.py`（15 分钟）

重点关注 `start()` 函数：

```python
@app.command()
def start(...):
    # 1. 加载配置
    # 2. 初始化 Provider
    # 3. 初始化 MessageBus
    # 4. 初始化 AgentLoop
    # 5. 初始化 Channels
    # 6. 启动事件循环
```

**思考题：**
- Q1: 配置是从哪个文件加载的？（提示：看 `ConfigLoader.from_env()`）
- Q2: 如果没有配置文件，会发生什么？

#### 文件 3: `README.md`（5 分钟）

快速浏览安装和使用说明，了解：
- 如何安装依赖
- 基本命令有哪些（`nanobot start`, `nanobot cron`, etc.）
- 配置文件在哪里

---

## 🔧 实践操作（40 分钟）

### 步骤 1：环境搭建（15 分钟）

#### 1.1 克隆项目（已完成 ✅）

你已经有了项目代码：`e:\hobby\momo\nanobot`

#### 1.2 创建虚拟环境

```powershell
# 进入项目目录
cd e:\hobby\momo\nanobot

# 创建虚拟环境
python -m venv venv

# 激活虚拟环境
.\venv\Scripts\Activate.ps1

# 如果遇到执行策略问题，运行：
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

#### 1.3 安装依赖

```powershell
# 开发模式安装（代码修改即生效）
pip install -e .

# 可选：安装开发依赖
pip install -e ".[dev]"
```

#### 1.4 验证安装

```powershell
# 查看命令帮助
nanobot --help

# 应该看到类似输出：
# Usage: nanobot [OPTIONS] COMMAND [ARGS]...
# Commands:
#   start   Start the nanobot agent
#   cron    Manage cron tasks
```

### 步骤 2：配置最小化运行环境（15 分钟）

#### 2.1 创建配置文件

在项目根目录创建 `.env` 文件：

```bash
# LLM Provider 配置（选择一个）
# 方案 1: 使用 OpenAI
PROVIDER=litellm
LITELLM_API_KEY=your-openai-api-key
LITELLM_MODEL=gpt-4o-mini

# 方案 2: 使用 DeepSeek（更便宜）
# PROVIDER=litellm
# LITELLM_API_KEY=your-deepseek-api-key
# LITELLM_BASE_URL=https://api.deepseek.com
# LITELLM_MODEL=deepseek-chat

# 工作目录
WORKSPACE_DIR=./workspace

# Channel 配置（CLI 模式不需要）
CHANNELS=cli
```

> ⚠️ **注意**：如果没有 API Key，可以先跳过这一步，后续章节会详细讲解如何获取。

#### 2.2 创建工作目录

```powershell
mkdir workspace
```

### 步骤 3：首次运行（10 分钟）

#### 方式 1：CLI 模式（推荐新手）

```powershell
# 启动 nanobot CLI 交互模式
nanobot start --channel cli

# 尝试简单对话
> 你好
> 帮我在 workspace 目录创建一个 hello.txt 文件，内容是 "Hello nanobot!"
> 列出 workspace 目录的文件
```

**观察要点：**
- ✅ LLM 是否正确响应
- ✅ 是否自动调用了工具（create_file）
- ✅ workspace 目录下是否生成了 hello.txt

#### 方式 2：查看日志

启动时观察日志输出：

```
2026-02-25 10:00:00.123 | INFO | nanobot.cli.commands:start:XX | Starting nanobot...
2026-02-25 10:00:00.456 | INFO | nanobot.agent.loop:__init__:XX | AgentLoop initialized
2026-02-25 10:00:00.789 | INFO | nanobot.channels.cli:start:XX | CLI channel started
```

**理解日志：**
- 第一列：时间戳
- 第二列：日志级别（INFO/DEBUG/ERROR）
- 第三列：代码位置（模块:函数:行号）
- 第四列：消息内容

---

## ✅ 本章练习

### 练习 1：代码导航练习

在 VS Code 中完成以下操作：

1. 找到 `nanobot start` 命令的入口函数（提示：`cli/commands.py`）
2. 找到 MessageBus 类的定义（提示：`bus/queue.py`）
3. 找到有多少个内置 Tool（提示：`agent/tools/` 目录下有几个文件）

### 练习 2：配置实验

尝试修改配置并观察效果：

1. 修改 `.env` 中的 `LITELLM_MODEL` 为其他模型（如果你有多个模型的 key）
2. 添加 `LOG_LEVEL=DEBUG` 配置，观察日志详细程度变化
3. 修改 `WORKSPACE_DIR`，看工具是否在新目录工作

### 练习 3：故障排查

人为制造错误并解决：

1. **实验 1**：删除 `.env` 文件，尝试启动 —— 观察错误提示
2. **实验 2**：将 API Key 改为无效值 —— 观察错误信息
3. **实验 3**：将 WORKSPACE_DIR 设为不存在的路径 —— 是否自动创建？

---

## 📝 本章总结

完成本章后，你应该能够：

- ✅ 理解 nanobot 的定位和架构概览
- ✅ 搭建好本地开发环境
- ✅ 成功运行 CLI 模式并与 Agent 对话
- ✅ 知道项目核心模块的目录分布
- ✅ 能够通过日志初步判断运行状态

**知识点检查清单：**
- [ ] 能说出 nanobot 的三个核心特性
- [ ] 能画出消息流转的简单流程图
- [ ] 知道 `.env` 配置文件的作用
- [ ] 理解 `pip install -e .` 的含义（开发模式）
- [ ] 能找到 CLI 命令的入口代码

---

## 🔜 下一章预告

[第 2 章：入口流程与 CLI 命令系统](./chapter02.md)

我们将深入研究：
- typer 如何构建 CLI 接口
- `nanobot start` 命令的完整启动流程
- 如何添加自定义命令
- 配置加载的详细机制

---

**学习进度：** [█░░░░░░░░░░░░░░░░░░░░] 1/21 章节完成

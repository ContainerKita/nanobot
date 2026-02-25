<div align="center">
  <img src="nanobot_logo.png" alt="nanobot" width="500">
  <h1>nanobot: 超轻量级个人 AI 助手</h1>
  <p>
    <a href="https://pypi.org/project/nanobot-ai/"><img src="https://img.shields.io/pypi/v/nanobot-ai" alt="PyPI"></a>
    <a href="https://pepy.tech/project/nanobot-ai"><img src="https://static.pepy.tech/badge/nanobot-ai" alt="Downloads"></a>
    <img src="https://img.shields.io/badge/python-≥3.11-blue" alt="Python">
    <img src="https://img.shields.io/badge/license-MIT-green" alt="License">
    <a href="./COMMUNICATION.md"><img src="https://img.shields.io/badge/Feishu-Group-E9DBFC?style=flat&logo=feishu&logoColor=white" alt="Feishu"></a>
    <a href="./COMMUNICATION.md"><img src="https://img.shields.io/badge/WeChat-Group-C5EAB4?style=flat&logo=wechat&logoColor=white" alt="WeChat"></a>
    <a href="https://discord.gg/MnCvHqpUGB"><img src="https://img.shields.io/badge/Discord-Community-5865F2?style=flat&logo=discord&logoColor=white" alt="Discord"></a>
  </p>
</div>

🐈 **nanobot** 是一个受 [Clawdbot](https://github.com/openclaw/openclaw) 启发的**超轻量级**个人 AI 助手

⚡️ 仅用 **约 4,000** 行代码就能提供核心智能体功能 — 比 Clawdbot 的 43 万多行代码**小 99%**。

📏 实时行数：**3,966 行**（随时运行 `bash core_agent_lines.sh` 验证）

## 📢 新闻

- **2026-02-24** 🚀 发布 **v0.1.4.post2** —— 以稳定性为核心：重构心跳机制、优化提示词缓存，并增强 provider 与 channel 的可靠性。详见[发布说明](https://github.com/HKUDS/nanobot/releases/tag/v0.1.4.post2)。
- **2026-02-23** 🔧 虚拟工具调用心跳、提示词缓存优化、Slack mrkdwn 修复。
- **2026-02-22** 🛡️ Slack 线程隔离、Discord typing 修复、智能体可靠性增强。
- **2026-02-21** 🎉 发布 **v0.1.4.post1** —— 新增 providers、多渠道媒体支持与大量稳定性改进。详见[发布说明](https://github.com/HKUDS/nanobot/releases/tag/v0.1.4.post1)。
- **2026-02-20** 🐦 飞书现已支持接收用户多模态文件，记忆系统底层更稳定。
- **2026-02-19** ✨ Slack 现可发送文件、Discord 自动分段长消息，CLI 模式支持 subagent。
- **2026-02-18** ⚡️ nanobot 新增 VolcEngine、MCP 自定义鉴权头与 Anthropic 提示词缓存支持。
- **2026-02-17** 🎉 发布 **v0.1.4** —— MCP 支持、进度流式输出、新 provider 及多渠道改进。详见[发布说明](https://github.com/HKUDS/nanobot/releases/tag/v0.1.4)。
- **2026-02-16** 🦞 nanobot 集成 [ClawHub](https://clawhub.ai) 技能，可搜索并安装公开技能。
- **2026-02-15** 🔑 nanobot 支持 OpenAI Codex provider（OAuth 登录）。
- **2026-02-14** 🔌 nanobot 支持 MCP！详见下方 [MCP 章节](#mcp-model-context-protocol)。
- **2026-02-13** 🎉 发布 **v0.1.3.post7** —— 包含安全加固与多项改进。**请升级到最新版本以修复安全问题**。详见[发布说明](https://github.com/HKUDS/nanobot/releases/tag/v0.1.3.post7)。
- **2026-02-12** 🧠 重构记忆系统 —— 代码更少、稳定性更高。欢迎参与[讨论](https://github.com/HKUDS/nanobot/discussions/566)！
- **2026-02-11** ✨ 增强 CLI 体验并新增 MiniMax 支持！

<details>
<summary>更早新闻</summary>

- **2026-02-10** 🎉 发布 **v0.1.3.post6**，包含多项改进！查看[更新说明](https://github.com/HKUDS/nanobot/releases/tag/v0.1.3.post6)与[路线图](https://github.com/HKUDS/nanobot/discussions/431)。
- **2026-02-09** 💬 新增 Slack、Email 和 QQ 支持 —— nanobot 现已支持多聊天平台。
- **2026-02-08** 🔧 重构 Provider 系统——新增 LLM provider 现在只需 2 步。详见下方配置章节。
- **2026-02-07** 🚀 发布 **v0.1.3.post5**，支持 Qwen 等关键改进！详见[这里](https://github.com/HKUDS/nanobot/releases/tag/v0.1.3.post5)。
- **2026-02-06** ✨ 新增 Moonshot/Kimi provider、Discord 集成及安全加固。
- **2026-02-05** ✨ 新增飞书 channel、DeepSeek provider，并增强定时任务支持。
- **2026-02-04** 🚀 发布 **v0.1.3.post4**，支持多 provider 与 Docker！详见[这里](https://github.com/HKUDS/nanobot/releases/tag/v0.1.3.post4)。
- **2026-02-03** ⚡ 集成 vLLM 以支持本地 LLM，并改进自然语言任务调度。
- **2026-02-02** 🎉 nanobot 正式发布！欢迎试用 🐈 nanobot！

</details>

## nanobot 的核心特性：

🪶 **超轻量级**：仅约 4,000 行代码 — 比 Clawdbot 小 99% - 核心功能完备。

🔬 **适合研究**：代码简洁易读，易于理解、修改和扩展，非常适合研究使用。

⚡️ **极速运行**：极小的体积意味着更快的启动速度、更低的资源占用和更快的迭代周期。

💎 **易于使用**：一键部署，开箱即用。

## 🏗️ 架构

<p align="center">
  <img src="nanobot_arch.png" alt="nanobot 架构" width="800">
</p>

## ✨ 功能特性

<table align="center">
  <tr align="center">
    <th><p align="center">📈 24/7 实时市场分析</p></th>
    <th><p align="center">🚀 全栈软件工程师</p></th>
    <th><p align="center">📅 智能日常计划管理</p></th>
    <th><p align="center">📚 个人知识助手</p></th>
  </tr>
  <tr>
    <td align="center"><p align="center"><img src="case/search.gif" width="180" height="400"></p></td>
    <td align="center"><p align="center"><img src="case/code.gif" width="180" height="400"></p></td>
    <td align="center"><p align="center"><img src="case/scedule.gif" width="180" height="400"></p></td>
    <td align="center"><p align="center"><img src="case/memory.gif" width="180" height="400"></p></td>
  </tr>
  <tr>
    <td align="center">发现 • 洞察 • 趋势</td>
    <td align="center">开发 • 部署 • 扩展</td>
    <td align="center">计划 • 自动化 • 组织</td>
    <td align="center">学习 • 记忆 • 推理</td>
  </tr>
</table>

## 📦 安装

**从源码安装**（最新功能，推荐开发使用）

```bash
git clone https://github.com/HKUDS/nanobot.git
cd nanobot
pip install -e .
```

**使用 [uv](https://github.com/astral-sh/uv) 安装**（稳定、快速）

```bash
uv tool install nanobot-ai
```

**从 PyPI 安装**（稳定版）

```bash
pip install nanobot-ai
```

## 🚀 快速开始

> [!TIP]
> 在 `~/.nanobot/config.json` 中设置你的 API 密钥。
> 获取 API 密钥：[OpenRouter](https://openrouter.ai/keys)（全球用户）· [Brave Search](https://brave.com/search/api/)（可选，用于网络搜索）

**1. 初始化**

```bash
nanobot onboard
```

**2. 配置**（`~/.nanobot/config.json`）

将下面 **两部分** 合并到配置中（其他配置都有默认值）。

*设置 API Key*（例如 OpenRouter，推荐全球用户）：
```json
{
  "providers": {
    "openrouter": {
      "apiKey": "sk-or-v1-xxx"
    }
  }
}
```

*设置模型*：
```json
{
  "agents": {
    "defaults": {
      "model": "anthropic/claude-opus-4-5"
    }
  }
}
```


**3. 聊天**

```bash
nanobot agent
```

就是这样！你在 2 分钟内就拥有了一个可用的 AI 助手。

## 🖥️ 本地模型（vLLM）

使用 vLLM 或任何兼容 OpenAI 的服务器运行 nanobot 的本地模型。

**1. 启动你的 vLLM 服务器**

```bash
vllm serve meta-llama/Llama-3.1-8B-Instruct --port 8000
```

**2. 配置**（`~/.nanobot/config.json`）

```json
{
  "providers": {
    "vllm": {
      "apiKey": "dummy",
      "apiBase": "http://localhost:8000/v1"
    }
  },
  "agents": {
    "defaults": {
      "model": "meta-llama/Llama-3.1-8B-Instruct"
    }
  }
}
```

**3. 聊天**

```bash
nanobot agent -m "你好，来自我的本地 LLM！"
```

> [!TIP]
> 对于不需要身份验证的本地服务器，`apiKey` 可以是任何非空字符串。

## 💬 聊天应用

将 nanobot 连接到你常用的聊天平台。

| 渠道 | 你需要准备 |
|---------|---------------|
| **Telegram** | 从 @BotFather 获取 Bot Token |
| **Discord** | Bot Token + Message Content Intent |
| **WhatsApp** | 扫描二维码绑定设备 |
| **Feishu** | App ID + App Secret |
| **Mochat** | Claw Token（支持自动配置） |
| **DingTalk** | App Key + App Secret |
| **Slack** | Bot Token + App-Level Token |
| **Email** | IMAP/SMTP 凭证 |
| **QQ** | App ID + App Secret |

<details>
<summary><b>Telegram</b>（推荐）</summary>

**1. 创建机器人**
- 打开 Telegram，搜索 `@BotFather`
- 发送 `/newbot`，按照提示操作
- 复制令牌

**2. 配置**

```json
{
  "channels": {
    "telegram": {
      "enabled": true,
      "token": "YOUR_BOT_TOKEN",
      "allowFrom": ["YOUR_USER_ID"]
    }
  }
}
```

> 你可以在 Telegram 设置中找到你的**用户 ID**，显示为 `@yourUserId`。
> 复制该值**不带 `@` 符号**并粘贴到配置文件中。

**3. 运行**

```bash
nanobot gateway
```

</details>

<details>
<summary><b>Mochat（Claw IM）</b></summary>

默认使用 **Socket.IO WebSocket**，并带有 HTTP 轮询回退。

**1. 让 nanobot 自动帮你配置 Mochat**

直接给 nanobot 发送下面这条消息（将 `xxx@xxx` 替换成你的真实邮箱）：

```
Read https://raw.githubusercontent.com/HKUDS/MoChat/refs/heads/main/skills/nanobot/skill.md and register on MoChat. My Email account is xxx@xxx Bind me as your owner and DM me on MoChat.
```

nanobot 会自动完成注册、写入 `~/.nanobot/config.json`，并连接到 Mochat。

**2. 重启网关**

```bash
nanobot gateway
```

到这里就完成了，剩下的都由 nanobot 自动处理。

<br>

<details>
<summary>手动配置（高级）</summary>

如果你更希望手动配置，把下面内容加入 `~/.nanobot/config.json`：

> 请妥善保管 `claw_token`。它只应通过 `X-Claw-Token` 请求头发送到你的 Mochat API 端点。

```json
{
  "channels": {
    "mochat": {
      "enabled": true,
      "base_url": "https://mochat.io",
      "socket_url": "https://mochat.io",
      "socket_path": "/socket.io",
      "claw_token": "claw_xxx",
      "agent_user_id": "6982abcdef",
      "sessions": ["*"],
      "panels": ["*"],
      "reply_delay_mode": "non-mention",
      "reply_delay_ms": 120000
    }
  }
}
```

</details>

</details>

<details>
<summary><b>Discord</b></summary>

**1. 创建机器人**
- 访问 https://discord.com/developers/applications
- 创建应用 → Bot → Add Bot
- 复制机器人令牌

**2. 启用意图**
- 在 Bot 设置中，启用 **MESSAGE CONTENT INTENT**
- （可选）如果你计划基于成员数据使用允许列表，启用 **SERVER MEMBERS INTENT**

**3. 获取你的用户 ID**
- Discord 设置 → 高级 → 启用**开发者模式**
- 右键点击你的头像 → **复制用户 ID**

**4. 配置**

```json
{
  "channels": {
    "discord": {
      "enabled": true,
      "token": "YOUR_BOT_TOKEN",
      "allowFrom": ["YOUR_USER_ID"]
    }
  }
}
```

**5. 邀请机器人**
- OAuth2 → URL Generator
- 范围：`bot`
- 机器人权限：`Send Messages`、`Read Message History`
- 打开生成的邀请 URL，将机器人添加到你的服务器

**6. 运行**

```bash
nanobot gateway
```

</details>

<details>
<summary><b>WhatsApp</b></summary>

需要 **Node.js ≥18**。

**1. 关联设备**

```bash
nanobot channels login
# 使用 WhatsApp 扫描二维码 → 设置 → 关联的设备
```

**2. 配置**

```json
{
  "channels": {
    "whatsapp": {
      "enabled": true,
      "allowFrom": ["+1234567890"]
    }
  }
}
```

**3. 运行**（两个终端）

```bash
# 终端 1
nanobot channels login

# 终端 2
nanobot gateway
```

</details>

<details>
<summary><b>飞书（Feishu）</b></summary>

使用 **WebSocket** 长连接 — 无需公网 IP。

**1. 创建飞书机器人**
- 访问 [飞书开放平台](https://open.feishu.cn/app)
- 创建新应用 → 启用**机器人**能力
- **权限**：添加 `im:message`（发送消息）
- **事件**：添加 `im.message.receive_v1`（接收消息）
  - 选择**长连接**模式（需要先运行 nanobot 建立连接）
- 从"凭证与基本信息"获取 **App ID** 和 **App Secret**
- 发布应用

**2. 配置**

```json
{
  "channels": {
    "feishu": {
      "enabled": true,
      "appId": "cli_xxx",
      "appSecret": "xxx",
      "encryptKey": "",
      "verificationToken": "",
      "allowFrom": []
    }
  }
}
```

> `encryptKey` 和 `verificationToken` 在长连接模式下是可选的。
> `allowFrom`：留空允许所有用户，或添加 `["ou_xxx"]` 限制访问。

**3. 运行**

```bash
nanobot gateway
```

> [!TIP]
> 飞书使用 WebSocket 接收消息 — 无需 webhook 或公网 IP！

</details>

<details>
<summary><b>QQ（QQ单聊）</b></summary>

使用 **botpy SDK** 与 WebSocket — 无需公网 IP。目前仅支持**私聊消息**。

**1. 注册并创建机器人**
- 访问 [QQ开放平台](https://q.qq.com) → 注册为开发者（个人或企业）
- 创建新的机器人应用
- 进入**开发设置** → 复制 **AppID** 和 **AppSecret**

**2. 设置沙箱测试**
- 在机器人管理控制台中，找到**沙箱配置**
- 在**在消息列表配置**下，点击**添加成员**并添加你自己的 QQ 号
- 添加后，用手机 QQ 扫描机器人的二维码 → 打开机器人资料 → 点击"发消息"开始聊天

**3. 配置**

> - `allowFrom`：留空以公开访问，或添加用户 openid 以限制。当用户给机器人发消息时，你可以在 nanobot 日志中找到 openid。
> - 生产环境：在机器人控制台提交审核并发布。详见 [QQ Bot 文档](https://bot.q.qq.com/wiki/)。

```json
{
  "channels": {
    "qq": {
      "enabled": true,
      "appId": "YOUR_APP_ID",
      "secret": "YOUR_APP_SECRET",
      "allowFrom": []
    }
  }
}
```

**4. 运行**

```bash
nanobot gateway
```

现在从 QQ 发送消息给机器人 — 它应该会回复！

</details>

<details>
<summary><b>钉钉（DingTalk）</b></summary>

使用 **Stream 模式** — 无需公网 IP。

**1. 创建钉钉机器人**
- 访问 [钉钉开放平台](https://open-dev.dingtalk.com/)
- 创建新应用 → 添加**机器人**能力
- **配置**：
  - 开启 **Stream 模式**
- **权限**：添加发送消息所需的权限
- 从"凭证"获取 **AppKey**（Client ID） 和 **AppSecret**（Client Secret）
- 发布应用

**2. 配置**

```json
{
  "channels": {
    "dingtalk": {
      "enabled": true,
      "clientId": "YOUR_APP_KEY",
      "clientSecret": "YOUR_APP_SECRET",
      "allowFrom": []
    }
  }
}
```

> `allowFrom`：留空允许所有用户，或添加 `["staffId"]` 限制访问。

**3. 运行**

```bash
nanobot gateway
```

</details>

<details>
<summary><b>Slack</b></summary>

使用 **Socket 模式** — 无需公网 URL。

**1. 创建 Slack 应用**
- 访问 [Slack API](https://api.slack.com/apps) → **Create New App** → "From scratch"
- 选择名称和工作区

**2. 配置应用**
- **Socket Mode**：开启 → 生成带有 `connections:write` 范围的 **App-Level Token** → 复制（`xapp-...`）
- **OAuth & Permissions**：添加机器人范围：`chat:write`、`reactions:write`、`app_mentions:read`
- **Event Subscriptions**：开启 → 订阅机器人事件：`message.im`、`message.channels`、`app_mention` → 保存更改
- **App Home**：滚动到 **Show Tabs** → 启用 **Messages Tab** → 勾选 **"Allow users to send Slash commands and messages from the messages tab"**
- **Install App**：点击 **Install to Workspace** → 授权 → 复制 **Bot Token**（`xoxb-...`）

**3. 配置 nanobot**

```json
{
  "channels": {
    "slack": {
      "enabled": true,
      "botToken": "xoxb-...",
      "appToken": "xapp-...",
      "groupPolicy": "mention"
    }
  }
}
```

**4. 运行**

```bash
nanobot gateway
```

直接 DM 机器人或在频道中 @提及它 — 它应该会回复！

> [!TIP]
> - `groupPolicy`：`"mention"`（默认 — 仅在 @提及时回复）、`"open"`（回复所有频道消息）或 `"allowlist"`（限制为特定频道）。
> - DM 策略默认开放。设置 `"dm": {"enabled": false}` 禁用 DM。

</details>

<details>
<summary><b>Email</b></summary>

给 nanobot 分配一个邮箱账户。它通过 **IMAP** 轮询收件，通过 **SMTP** 回复 — 像一个个人邮件助手。

**1. 获取凭证（Gmail 示例）**
- 为你的机器人创建一个专用的 Gmail 账户（例如 `my-nanobot@gmail.com`）
- 启用两步验证 → 创建[应用密码](https://myaccount.google.com/apppasswords)
- 将此应用密码用于 IMAP 和 SMTP

**2. 配置**

> - `consentGranted` 必须为 `true` 才能允许邮箱访问。这是一个安全门 — 设置 `false` 完全禁用。
> - `allowFrom`：留空接受所有人的邮件，或限制为特定发件人。
> - `smtpUseTls` 和 `smtpUseSsl` 默认为 `true` / `false`，这对 Gmail（端口 587 + STARTTLS）是正确的。无需显式设置。
> - 如果你只想读取/分析邮件而不发送自动回复，设置 `"autoReplyEnabled": false`。

```json
{
  "channels": {
    "email": {
      "enabled": true,
      "consentGranted": true,
      "imapHost": "imap.gmail.com",
      "imapPort": 993,
      "imapUsername": "my-nanobot@gmail.com",
      "imapPassword": "your-app-password",
      "smtpHost": "smtp.gmail.com",
      "smtpPort": 587,
      "smtpUsername": "my-nanobot@gmail.com",
      "smtpPassword": "your-app-password",
      "fromAddress": "my-nanobot@gmail.com",
      "allowFrom": ["your-real-email@gmail.com"]
    }
  }
}
```


**3. 运行**

```bash
nanobot gateway
```

</details>

## 🌐 Agent 社交网络

🐈 nanobot 可以接入智能体社交网络（agent community）。**只要发一条消息，就能自动加入！**

| 平台 | 如何加入（把这条消息发给你的 bot） |
|----------|-------------|
| [**Moltbook**](https://www.moltbook.com/) | `Read https://moltbook.com/skill.md and follow the instructions to join Moltbook` |
| [**ClawdChat**](https://clawdchat.ai/) | `Read https://clawdchat.ai/skill.md and follow the instructions to join ClawdChat` |

你只需在 CLI 或任意聊天渠道里把上面的命令发给 nanobot，剩下它会自动完成。

## ⚙️ 配置

配置文件：`~/.nanobot/config.json`

### 提供商

> [!TIP]
> - **Groq** 通过 Whisper 提供免费语音转录。配置后，Telegram 语音消息可自动转文字。
> - **智谱编程计划**：在 zhipu provider 中设置 `"apiBase": "https://open.bigmodel.cn/api/coding/paas/v4"`。
> - **MiniMax（中国大陆）**：如果密钥来自 minimaxi.com，在 minimax provider 中设置 `"apiBase": "https://api.minimaxi.com/v1"`。
> - **火山引擎编程计划**：在 volcengine provider 中设置 `"apiBase": "https://ark.cn-beijing.volces.com/api/coding/v3"`。

| 提供商 | 用途 | 获取 API Key |
|----------|---------|-------------|
| `custom` | 任意兼容 OpenAI 的端点（直连，不走 LiteLLM） | — |
| `openrouter` | LLM（推荐，可访问多模型） | [openrouter.ai](https://openrouter.ai) |
| `anthropic` | LLM（Claude 直连） | [console.anthropic.com](https://console.anthropic.com) |
| `openai` | LLM（GPT 直连） | [platform.openai.com](https://platform.openai.com) |
| `deepseek` | LLM（DeepSeek 直连） | [platform.deepseek.com](https://platform.deepseek.com) |
| `groq` | LLM + **语音转录**（Whisper） | [console.groq.com](https://console.groq.com) |
| `gemini` | LLM（Gemini 直连） | [aistudio.google.com](https://aistudio.google.com) |
| `minimax` | LLM（MiniMax 直连） | [platform.minimaxi.com](https://platform.minimaxi.com) |
| `aihubmix` | LLM（API 网关，可访问多模型） | [aihubmix.com](https://aihubmix.com) |
| `siliconflow` | LLM（SiliconFlow/硅基流动） | [siliconflow.cn](https://siliconflow.cn) |
| `volcengine` | LLM（VolcEngine/火山引擎） | [volcengine.com](https://www.volcengine.com) |
| `dashscope` | LLM（Qwen） | [dashscope.console.aliyun.com](https://dashscope.console.aliyun.com) |
| `moonshot` | LLM（Moonshot/Kimi） | [platform.moonshot.cn](https://platform.moonshot.cn) |
| `zhipu` | LLM（智谱 GLM） | [open.bigmodel.cn](https://open.bigmodel.cn) |
| `vllm` | LLM（本地，兼容 OpenAI 服务） | — |
| `openai_codex` | LLM（Codex，OAuth） | `nanobot provider login openai-codex` |
| `github_copilot` | LLM（GitHub Copilot，OAuth） | `nanobot provider login github-copilot` |

<details>
<summary><b>OpenAI Codex（OAuth）</b></summary>

Codex 使用 OAuth 而不是 API Key。需要 ChatGPT Plus 或 Pro 账户。

**1. 登录：**
```bash
nanobot provider login openai-codex
```

**2. 设置模型**（合并到 `~/.nanobot/config.json`）：
```json
{
  "agents": {
    "defaults": {
      "model": "openai-codex/gpt-5.1-codex"
    }
  }
}
```

**3. 聊天：**
```bash
nanobot agent -m "Hello!"
```

> Docker 用户：交互式 OAuth 登录请使用 `docker run -it`。

</details>

<details>
<summary><b>自定义 Provider（任意 OpenAI 兼容 API）</b></summary>

可直接连接任意兼容 OpenAI 的端点：LM Studio、llama.cpp、Together AI、Fireworks、Azure OpenAI 或自建服务。该模式绕过 LiteLLM，模型名原样透传。

```json
{
  "providers": {
    "custom": {
      "apiKey": "your-api-key",
      "apiBase": "https://api.your-provider.com/v1"
    }
  },
  "agents": {
    "defaults": {
      "model": "your-model-name"
    }
  }
}
```

> 对于不需要鉴权的本地服务，把 `apiKey` 设为任意非空字符串即可（如 `"no-key"`）。

</details>

<details>
<summary><b>vLLM（本地 / OpenAI 兼容）</b></summary>

使用 vLLM 或任意 OpenAI 兼容服务运行本地模型：

**1. 启动服务**（示例）：
```bash
vllm serve meta-llama/Llama-3.1-8B-Instruct --port 8000
```

**2. 写入配置**（片段，合并到 `~/.nanobot/config.json`）：

*Provider（本地可用任意非空 key）：*
```json
{
  "providers": {
    "vllm": {
      "apiKey": "dummy",
      "apiBase": "http://localhost:8000/v1"
    }
  }
}
```

*模型：*
```json
{
  "agents": {
    "defaults": {
      "model": "meta-llama/Llama-3.1-8B-Instruct"
    }
  }
}
```

</details>

<details>
<summary><b>添加新提供商（开发者指南）</b></summary>

nanobot 使用 **Provider Registry**（`nanobot/providers/registry.py`）作为单一真相来源。
添加新 provider 只需 **2 步**，无需改 if-elif 链。

**步骤 1.** 在 `nanobot/providers/registry.py` 的 `PROVIDERS` 添加 `ProviderSpec`：

```python
ProviderSpec(
    name="myprovider",                   # 配置字段名
    keywords=("myprovider", "mymodel"),  # 模型名关键词（自动匹配）
    env_key="MYPROVIDER_API_KEY",        # LiteLLM 环境变量
    display_name="My Provider",          # `nanobot status` 显示名
    litellm_prefix="myprovider",         # 自动前缀：model -> myprovider/model
    skip_prefixes=("myprovider/",),      # 避免重复前缀
)
```

**步骤 2.** 在 `nanobot/config/schema.py` 的 `ProvidersConfig` 中添加字段：

```python
class ProvidersConfig(BaseModel):
    ...
    myprovider: ProviderConfig = ProviderConfig()
```

就完成了！环境变量注入、模型名前缀、配置匹配和 `nanobot status` 展示都会自动生效。

**常用 `ProviderSpec` 选项：**

| 字段 | 描述 | 示例 |
|-------|-------------|---------|
| `litellm_prefix` | 给 LiteLLM 自动补模型前缀 | `"dashscope"` → `dashscope/qwen-max` |
| `skip_prefixes` | 模型已带前缀时不重复补 | `("dashscope/", "openrouter/")` |
| `env_extras` | 额外要设置的环境变量 | `(("ZHIPUAI_API_KEY", "{api_key}"),)` |
| `model_overrides` | 针对特定模型覆写参数 | `(("kimi-k2.5", {"temperature": 1.0}),)` |
| `is_gateway` | 是否可路由任意模型（如 OpenRouter） | `True` |
| `detect_by_key_prefix` | 通过 API Key 前缀识别网关 | `"sk-or-"` |
| `detect_by_base_keyword` | 通过 API Base URL 关键字识别网关 | `"openrouter"` |
| `strip_model_prefix` | 重新补前缀前先去掉已有前缀 | `True`（AiHubMix） |

</details>

### MCP (Model Context Protocol)

> [!TIP]
> 配置格式兼容 Claude Desktop / Cursor，你可以直接复制 MCP 服务器 README 中的配置。

nanobot 支持 [MCP](https://modelcontextprotocol.io/)：可连接外部工具服务器，并像原生工具一样调用。

在 `config.json` 中添加 MCP 服务器：

```json
{
  "tools": {
    "mcpServers": {
      "filesystem": {
        "command": "npx",
        "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/dir"]
      },
      "my-remote-mcp": {
        "url": "https://example.com/mcp/",
        "headers": {
          "Authorization": "Bearer xxxxx"
        }
      }
    }
  }
}
```

支持两种传输方式：

| 模式 | 配置 | 示例 |
|------|--------|---------|
| **Stdio** | `command` + `args` | 本地进程（`npx` / `uvx`） |
| **HTTP** | `url` + `headers`（可选） | 远端端点（`https://mcp.example.com/sse`） |

若某些服务较慢，可用 `toolTimeout` 覆盖默认 30 秒超时：

```json
{
  "tools": {
    "mcpServers": {
      "my-slow-server": {
        "url": "https://example.com/mcp/",
        "toolTimeout": 120
      }
    }
  }
}
```

启动后会自动发现并注册 MCP 工具，LLM 可与内置工具一起使用，无需额外配置。

### 安全

> [!TIP]
> 生产部署建议在配置中设置 `"restrictToWorkspace": true`，将 agent 沙箱化。

| 选项 | 默认值 | 描述 |
|--------|---------|-------------|
| `tools.restrictToWorkspace` | `false` | 设为 `true` 后，将 **所有** agent 工具（shell、文件读/写/编辑、列表）限制在工作区目录内，防止路径穿越和越权访问。 |
| `channels.*.allowFrom` | `[]`（允许所有） | 用户 ID 白名单。空数组=允许所有，非空=仅允许列表用户交互。 |

## CLI 参考

| 命令 | 描述 |
|---------|-------------|
| `nanobot onboard` | 初始化配置和工作区 |
| `nanobot agent -m "..."` | 与智能体聊天 |
| `nanobot agent` | 交互式聊天模式 |
| `nanobot agent --no-markdown` | 显示纯文本回复 |
| `nanobot agent --logs` | 聊天时显示运行日志 |
| `nanobot gateway` | 启动网关 |
| `nanobot status` | 显示状态 |
| `nanobot provider login openai-codex` | Provider OAuth 登录 |
| `nanobot channels login` | 关联 WhatsApp（扫码） |
| `nanobot channels status` | 显示渠道状态 |

交互模式退出方式：`exit`、`quit`、`/exit`、`/quit`、`:q` 或 `Ctrl+D`。

<details>
<summary><b>定时任务（Cron）</b></summary>

```bash
# 添加任务
nanobot cron add --name "daily" --message "Good morning!" --cron "0 9 * * *"
nanobot cron add --name "hourly" --message "Check status" --every 3600

# 查看任务
nanobot cron list

# 删除任务
nanobot cron remove <job_id>
```

</details>

<details>
<summary><b>Heartbeat（周期任务）</b></summary>

网关每 30 分钟会唤醒一次，并检查工作区中的 `HEARTBEAT.md`（`~/.nanobot/workspace/HEARTBEAT.md`）。
若文件里有任务，agent 会执行并将结果发送到你最近活跃的聊天渠道。

**设置方式：**编辑 `~/.nanobot/workspace/HEARTBEAT.md`（`nanobot onboard` 会自动创建）：

```markdown
## Periodic Tasks

- [ ] Check weather forecast and send a summary
- [ ] Scan inbox for urgent emails
```

你也可以直接让 agent 维护这个文件，例如让它“添加一个周期任务”。

> **注意：**需要网关在运行（`nanobot gateway`），并且你至少和 bot 聊过一次，这样它才知道把结果发到哪个渠道。

</details>

## 🐳 Docker

> [!TIP]
> `-v ~/.nanobot:/root/.nanobot` 会把本地配置目录挂载进容器，确保配置与工作区在重启后仍然保留。

### Docker Compose

```bash
docker compose run --rm nanobot-cli onboard   # 首次初始化
vim ~/.nanobot/config.json                     # 填写 API keys
docker compose up -d nanobot-gateway           # 启动网关
```

```bash
docker compose run --rm nanobot-cli agent -m "Hello!"   # 运行 CLI
docker compose logs -f nanobot-gateway                   # 查看日志
docker compose down                                      # 停止
```

### Docker

```bash
# 构建镜像
docker build -t nanobot .

# 初始化配置（仅首次）
docker run -v ~/.nanobot:/root/.nanobot --rm nanobot onboard

# 在主机编辑配置，填入 API Key
vim ~/.nanobot/config.json

# 启动网关（连接已启用渠道，如 Telegram/Discord/Mochat）
docker run -v ~/.nanobot:/root/.nanobot -p 18790:18790 nanobot gateway

# 或执行单次命令
docker run -v ~/.nanobot:/root/.nanobot --rm nanobot agent -m "Hello!"
docker run -v ~/.nanobot:/root/.nanobot --rm nanobot status
```

## 🐧 Linux 服务

可将网关作为 systemd 用户服务运行，实现开机自动启动与故障自动重启。

**1. 查找 nanobot 可执行文件路径：**

```bash
which nanobot   # 例如 /home/user/.local/bin/nanobot
```

**2. 创建服务文件** `~/.config/systemd/user/nanobot-gateway.service`（必要时替换 `ExecStart`）：

```ini
[Unit]
Description=Nanobot Gateway
After=network.target

[Service]
Type=simple
ExecStart=%h/.local/bin/nanobot gateway
Restart=always
RestartSec=10
NoNewPrivileges=yes
ProtectSystem=strict
ReadWritePaths=%h

[Install]
WantedBy=default.target
```

**3. 启用并启动：**

```bash
systemctl --user daemon-reload
systemctl --user enable --now nanobot-gateway
```

**常用操作：**

```bash
systemctl --user status nanobot-gateway        # 查看状态
systemctl --user restart nanobot-gateway       # 配置改动后重启
journalctl --user -u nanobot-gateway -f        # 持续查看日志
```

如果你修改了 `.service` 文件本身，重启前先执行 `systemctl --user daemon-reload`。

> **注意：**用户服务默认仅在登录期间运行。若要注销后继续运行，请启用 lingering：
>
> ```bash
> loginctl enable-linger $USER
> ```

## 📁 项目结构

```
nanobot/
├── agent/          # 🧠 核心智能体逻辑
│   ├── loop.py     #    智能体循环（LLM ↔ 工具执行）
│   ├── context.py  #    提示词构建器
│   ├── memory.py   #    持久化记忆
│   ├── skills.py   #    技能加载器
│   ├── subagent.py #    后台任务执行
│   └── tools/      #    内置工具（含 spawn）
├── skills/         # 🎯 内置技能（github、weather、tmux...）
├── channels/       # 📱 聊天渠道集成
├── bus/            # 🚌 消息路由
├── cron/           # ⏰ 定时任务
├── heartbeat/      # 💓 主动唤醒
├── providers/      # 🤖 LLM providers（OpenRouter 等）
├── session/        # 💬 会话管理
├── config/         # ⚙️ 配置
└── cli/            # 🖥️ 命令
```

## 🤝 贡献与路线图

欢迎提交 PR！代码库刻意保持小而清晰。🤗

**路线图** —— 任选一项并[发起 PR](https://github.com/HKUDS/nanobot/pulls)！

- [ ] **多模态** —— 图像、语音、视频
- [ ] **长期记忆** —— 不遗忘关键上下文
- [ ] **更强推理** —— 多步规划与反思
- [ ] **更多集成** —— 日历等更多能力
- [ ] **自我改进** —— 从反馈与错误中学习

### 贡献者

<a href="https://github.com/HKUDS/nanobot/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=HKUDS/nanobot&max=100&columns=12&updated=20260210" alt="Contributors" />
</a>


## ⭐ Star 历史

<div align="center">
  <a href="https://star-history.com/#HKUDS/nanobot&Date">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/svg?repos=HKUDS/nanobot&type=Date&theme=dark" />
      <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/svg?repos=HKUDS/nanobot&type=Date" />
      <img alt="Star History Chart" src="https://api.star-history.com/svg?repos=HKUDS/nanobot&type=Date" style="border-radius: 15px; box-shadow: 0 0 30px rgba(0, 217, 255, 0.3);" />
    </picture>
  </a>
</div>

<p align="center">
  <em> 感谢访问 ✨ nanobot！</em><br><br>
  <img src="https://visitor-badge.laobi.icu/badge?page_id=HKUDS.nanobot&style=for-the-badge&color=00d4ff" alt="访问量">
</p>


<p align="center">
  <sub>nanobot 仅用于教育、研究和技术交流目的</sub>
</p>

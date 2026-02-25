# 第 17 章：添加新 Provider

**预计学习时间：2 小时**  
**难度：⭐⭐⭐⭐**

---

## 📚 本章目标

- 理解 Provider 抽象及注册机制
- 学会接入一个本地模型 Provider（以 Ollama 为例）
- 掌握流式输出与非流式输出处理思路
- 能够为 Provider 补充基础测试与降级逻辑

---

## 📖 理论学习（35 分钟）

### 1. Provider 的定位

Provider 层负责屏蔽不同模型平台差异，为上层提供统一调用接口。

主要价值：

- 统一 `chat` 调用形态
- 统一错误模型（网络、鉴权、限流）
- 统一流式/非流式响应拼接

### 2. 注册机制

核心位置：

- `nanobot/providers/base.py`
- `nanobot/providers/registry.py`

新增 Provider 的常见步骤：

1. 实现 Provider 类
2. 在 registry 中注册元信息
3. 在 CLI 创建 Provider 的流程中纳入分支

### 3. 本地模型接入关注点

以 Ollama 为例，需重点关注：

- 模型可用性检查
- 请求超时策略
- 大响应场景下的输出截断或流式分段

---

## 💻 代码阅读（45 分钟）

### 任务 1：阅读抽象与默认实现（20 分钟）

打开：

- `nanobot/providers/base.py`
- `nanobot/providers/litellm_provider.py`

重点关注：

- `chat()` 输入输出契约
- 错误处理和参数透传方式

### 任务 2：阅读可替换实现（15 分钟）

打开：

- `nanobot/providers/custom_provider.py`
- `nanobot/providers/openai_codex_provider.py`

重点关注：

- 自定义 HTTP 调用模式
- provider 选择与 model 映射

### 任务 3：阅读注册流程（10 分钟）

打开：`nanobot/providers/registry.py`

重点关注：

- provider 元信息结构
- 查询和回退策略

---

## 🔧 实践操作（40 分钟）

### 练习 1：实现 Ollama Provider（20 分钟）

要求：

- 新建 Provider 文件
- 实现最小 `chat()`
- 支持模型名与基础 URL 配置

### 练习 2：接入命令入口（10 分钟）

要求：

- 在 provider 创建逻辑中识别并实例化 Ollama Provider
- 保持已有 provider 行为不变

### 练习 3：验证与降级（10 分钟）

验证场景：

- Ollama 服务可用：正常回复
- Ollama 不可用：给出可读错误，不阻断进程

---

## ✅ 本章检查清单

- [ ] 我能解释 Provider 抽象与注册机制
- [ ] 我完成了 Ollama Provider 的最小接入
- [ ] 我处理了至少 1 个网络/超时异常
- [ ] 我完成了基本可用性验证

---

**学习进度：** [█████████████████░░░░] 17/21 章节  
**下一章：** [第 18 章：开发 Skill 技能包](./chapter18.md)

# 第 6 章：Provider 抽象与 LLM 集成

**预计学习时间：2 小时** | **难度：⭐⭐**

## 📚 本章目标
- 理解 Provider 的抽象层设计
- 掌握 LiteLLM 的统一调用方式
- 学习消息格式转换（OpenAI 格式）
- 理解流式响应的实现

## 核心内容

### Provider 架构
```
BaseProvider (抽象接口)
    ↓
LiteLLMProvider (通用实现)
    ↓
litellm 库 → OpenAI/DeepSeek/Claude/etc.
```

### 代码阅读路线
- [providers/base.py](../../nanobot/providers/base.py) - 基类定义
- [providers/litellm_provider.py](../../nanobot/providers/litellm_provider.py) - LiteLLM 实现
- [providers/registry.py](../../nanobot/providers/registry.py) - Provider 注册

## 实践练习
1. 切换不同的 LLM 模型，对比响应质量
2. 启用 streaming，观察流式输出
3. 添加自定义 Provider（如本地 vLLM）

---
**学习进度：** [██████░░░░░░░░░░░░░░] 6/21 章节

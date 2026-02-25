# 常见问题 FAQ

## 环境配置问题

### Q1: pip install -e . 报错找不到模块？

**可能原因：**
- Python 版本不是 3.11+
- 虚拟环境未激活

**解决方案：**
```powershell
# 检查 Python 版本
python --version  # 应该是 3.11 或更高

# 确保在虚拟环境中
.\venv\Scripts\Activate.ps1

# 重新安装
pip install -e .
```

### Q2: nanobot 命令找不到？

**解决方案：**
```powershell
# 确认虚拟环境激活
Get-Command nanobot

# 如果找不到，尝试直接运行
python -m nanobot --help
```

### Q3: .env 文件不生效？

**检查清单：**
- [ ] 文件名是 `.env`（不是 `env.txt` 或其他）
- [ ] 文件在项目根目录（与 pyproject.toml 同级）
- [ ] 参数名正确（如 `LITELLM_API_KEY` 不是 `API_KEY`）
- [ ] 没有引号（`VALUE=123` 不是 `VALUE="123"`）

---

## 运行问题

### Q4: Agent 不回复消息？

**排查步骤：**
1. 检查日志级别：`LOG_LEVEL=DEBUG`
2. 查看是否有错误日志
3. 确认 API Key 有效
4. 测试网络连接（`curl https://api.openai.com`）

### Q5: 达到最大迭代次数？

**原因：**任务太复杂或 LLM 陷入循环

**解决方案：**
- 简化任务描述
- 调整 max_iterations（但不建议过大）
- 检查工具返回结果是否清晰
- 尝试更强的模型（gpt-4 vs gpt-3.5）

### Q6: Telegram Bot 收不到消息？

**检查清单：**
- [ ] Token 正确（从 @BotFather 获取）
- [ ] `.env` 中配置了 `TELEGRAM_TOKEN=xxx`
- [ ] `CHANNELS=telegram` 或包含 telegram
- [ ] 给 Bot 发送了 /start 命令
- [ ] 网络能访问 Telegram API

---

## 开发问题

### Q7: 如何调试工具执行？

```python
# 在 agent/loop.py 的 _execute_tool 添加：
async def _execute_tool(self, tool_call):
    logger.info(f"🛠️  Executing: {tool_call.name}")
    logger.debug(f"   Arguments: {tool_call.arguments}")
    
    result = await self.tools.get(tool_call.name).execute(**tool_call.arguments)
    
    logger.debug(f"   Result: {result[:200]}...")  # 截断长结果
    return result
```

### Q8: 如何测试新功能不影响现有代码？

**使用 Git 分支：**
```powershell
# 创建新分支
git checkout -b feature/my-tool

# 开发...

# 测试通过后合并
git checkout main
git merge feature/my-tool
```

### Q9: ruff 检查报错怎么办？

```powershell
# 自动修复大部分问题
ruff check --fix .

# 查看详细错误
ruff check nanobot/
```

---

## 学习问题

### Q10: 异步编程不理解？

**推荐资源：**
- [Real Python - Async IO](https://realpython.com/async-io-python/)
- [官方文档](https://docs.python.org/3/library/asyncio.html)

**核心概念：**
- `async def`: 定义协程函数
- `await`: 等待协程完成
- `asyncio.gather()`: 并发运行多个协程

### Q11: Pydantic 校验不理解？

**核心示例：**
```python
from pydantic import BaseModel, Field

class Config(BaseModel):
    name: str = Field(..., min_length=1)  # 必填，至少1个字符
    age: int = Field(default=0, ge=0)     # 默认0，≥0
    
config = Config(name="Alice", age=25)  # ✅
config = Config(name="", age=-1)       # ❌ ValidationError
```

### Q12: 如何快速找到某个功能的代码？

**方法 1：全局搜索**
```powershell
# VS Code: Ctrl+Shift+F
# 搜索关键字，如 "write_file"
```

**方法 2：追踪调用链**
```python
# 从入口开始：
nanobot start → cli/commands.py → start()
→ AgentLoop → agent/loop.py → run()
→ _execute_tool() → tools/filesystem.py
```

---

## 获取帮助

如果以上 FAQ 没有解决你的问题：

1. **搜索 GitHub Issues**：https://github.com/HKUDS/nanobot/issues
2. **提问 Discussions**：https://github.com/HKUDS/nanobot/discussions
3. **加入 Discord**：社区链接见 README
4. **提交新 Issue**：详细描述问题、错误日志、环境信息

---

**最后更新：2026-02-25**

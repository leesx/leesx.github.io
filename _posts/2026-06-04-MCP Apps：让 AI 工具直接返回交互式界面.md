# MCP Apps：让 AI 工具直接返回交互式界面

## 背景

Model Context Protocol 官方博客在 2026-01-26 发布了 `MCP Apps - Bringing UI Capabilities To MCP Clients`。这篇文章的重要性不在于“又多了一个扩展”，而在于它补上了 MCP 长期缺的一块：**tool 不再只能回文本或结构化数据，而是可以直接把交互式界面嵌进对话。**

官方把 MCP Apps 定义为第一个正式的 MCP 扩展。工具现在可以返回表单、可视化、地图、仪表盘、多步骤工作流等 UI，并由 host 在 sandboxed iframe 中直接渲染。对 agent 产品来说，这相当于把“会话式工具调用”推进到了“会话式应用界面”。

## 为什么重要

- **MCP 从“工具协议”开始走向“应用协议”**：过去 tool call 适合读写数据，但不适合让用户做选择、确认和探索；MCP Apps 正在补这个空白。
- **人机协作终于有了标准交互层**：当工作流里必须让用户选项、授权、确认或拖拽时，不必继续把对话写成冗长 prompt。
- **跨客户端复用开始成立**：官方文章明确提到，ChatGPT、Claude、Goose 和 VS Code 都已支持或开始支持这项能力，开发者不必为每个客户端单独写 UI 协议。

## 前端/AI 开发落地建议

### 1. 把 MCP Apps 用在“文本不够用”的节点

最适合切入 MCP Apps 的，不是普通问答，而是这些场景：

- 参数很多的配置向导
- 需要筛选和 drill-down 的数据分析
- 需要人工确认的审批流
- 需要多步输入的表单式任务

如果一个流程已经开始出现“模型解释一大段，用户再回一句 yes/no”这种摩擦，通常就该考虑换成 UI。

### 2. 前端要把 UI 当成 agent 工作流的一部分

MCP Apps 不是把一个 iframe 塞进聊天窗口就结束了。官方强调，App 可以：

- 调用服务器工具
- 更新模型上下文
- 发送后续消息推进会话

这意味着前端设计时要想清楚三层边界：

- 哪些状态留在 UI 本地
- 哪些状态要回写给模型
- 哪些动作必须经过 host 审批

### 3. 优先设计“可审计的 UI-to-tool 通路”

MCP Apps 官方安全模型里，重点不是“绝对信任 UI”，而是：

- sandboxed iframe
- 预声明模板
- 可记录的 JSON-RPC 消息
- UI 发起工具调用时可要求用户同意

工程上建议把这条通路可视化：让用户知道当前界面触发了什么工具、提交了哪些参数、是否会产生副作用。

## 注意事项

- **不要把 MCP Apps 当成传统前端嵌入页**：它服务的是 agent-host 语境，关键在上下文同步、工具调用和审批边界。
- **安全模型依赖 host，而不是只依赖 app 作者自觉**：sandbox、消息审计和 consent 都应该是默认能力。
- **适配性仍然要验证**：虽然官方列出了多个已支持客户端，但不同 host 的 UI 细节、权限策略和调试体验仍可能不同。

## 来源链接

- Model Context Protocol Blog：MCP Apps - Bringing UI Capabilities To MCP Clients（2026-01-26）  
  https://blog.modelcontextprotocol.io/posts/2026-01-26-mcp-apps/
- MCP Apps Quickstart  
  https://apps.extensions.modelcontextprotocol.io/

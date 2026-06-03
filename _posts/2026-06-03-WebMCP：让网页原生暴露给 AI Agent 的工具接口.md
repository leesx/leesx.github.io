# WebMCP：让网页原生暴露给 AI Agent 的工具接口

## 背景

Chrome for Developers 在 2026-05-18 发布了 `WebMCP` 文档，并在 2026-05-28 更新内容。它把一个很关键的问题正式提上台面：如果未来越来越多的 AI Agent 会直接在浏览器里帮用户完成任务，网页是不是应该原生暴露“可调用的工具”，而不是只让 Agent 靠截图、DOM 结构和鼠标点击去猜？

WebMCP 的方向，就是让网页通过 JavaScript API 或带注解的 HTML form，把页面能力显式声明成 tool，并附带 JSON Schema、状态信息与权限边界。这样 Agent 可以更准确地理解“这个页面到底能做什么”。

## 为什么重要

- **把 Agent 从“猜 UI”拉回“调用能力”**：官方明确指出，网站可以声明搜索、下单、筛选等工具用途，而不是让 Agent 逐个元素推断按钮含义。
- **减少复杂流程里的误操作**：对表单填写、日期选择、售后支持、旅行预订这类多步骤流程，显式 tool 比单纯 actuation 更稳定。
- **前端开始承担“Agent 接口层”职责**：过去前端更多暴露给人类用户；WebMCP 让前端第一次需要认真设计“给浏览器内 Agent 使用的语义化能力”。

## 前端/AI 开发落地建议

### 1. 先挑高价值且结构清晰的页面做试点

官方示例最适合的，不是信息展示页，而是：

- 结构化表单
- 多步骤预订/申请流程
- 复杂筛选器
- 开发者设置页或诊断入口

如果你的产品有“报销申请”“工单提交”“筛选商品”“预订资源”这类固定流程，可以优先抽出一个最容易标准化的入口做 PoC。

### 2. 把 tool 设计成“可解释、可回退、可确认”

WebMCP 的价值不只是给 Agent 一个入口，更重要的是让调用结果对用户可见。工程上建议：

- 输入输出都尽量结构化，避免把歧义留给模型
- 高风险动作加确认步骤，例如支付、删除、提交不可逆变更
- 明确页面状态，让 Agent 知道当前是否可提交、是否缺字段、是否需要人工确认

### 3. 不要把 WebMCP 当成替代后端 MCP

Chrome 官方单独写了“何时用 WebMCP，何时用 MCP”，核心含义很明确：WebMCP 解决的是 **网页内可见交互**，不是替代服务端工具协议。适合网页里的能力，仍然放在页面里；适合跨系统取数、写库、跑后台任务的，继续留在 MCP 或其他服务端工具层。

## 注意事项

- **这是早期能力，不适合直接大规模上线**：官方说明目前可通过 Chrome flag 本地开发，并计划在 Chrome 149 提供 origin trial。
- **必须有浏览器上下文**：官方明确写到，WebMCP 的 tool 调用依赖浏览器标签页或 webview，不支持纯 headless 场景。
- **复杂站点接入成本不低**：如果页面状态很多、交互链条复杂，往往要补状态同步和额外 JavaScript，而不是简单加几个属性就完成。

## 来源链接

- WebMCP（Chrome for Developers，发布于 2026-05-18，更新于 2026-05-28）  
  https://developer.chrome.com/docs/ai/webmcp?hl=en
- When to use WebMCP and MCP（Chrome for Developers，发布于 2026-03-11，更新于 2026-05-19）  
  https://developer.chrome.com/blog/webmcp-mcp-usage?hl=en
- WebMCP explainer（GitHub）  
  https://github.com/webmachinelearning/webmcp

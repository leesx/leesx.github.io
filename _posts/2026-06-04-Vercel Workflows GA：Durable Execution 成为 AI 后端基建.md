# Vercel Workflows GA：Durable Execution 成为 AI 后端基建

## 背景

Vercel 在 2026-04-16 发布了 `A new programming model for durable execution`，并宣布 **Vercel Workflows 正式 GA**。这篇文章值得前端和 AI 工程团队关注，不只是因为它又发布了一个后端产品，而是因为它试图解决 AI 应用最常见的一类生产问题：

**原型阶段的 agent 或长任务流程，往往能在本地跑通，但一到线上就被超时、重试、状态丢失、队列编排和观测性拖垮。**

Vercel 的答案是，把 durable execution 直接做成应用代码的一部分。你在 TypeScript 里用 `"use workflow"` 和 `"use step"` 标记流程和步骤，底层的队列、状态持久化、重试和观测交给平台处理。

## 为什么重要

- **AI 后端的复杂度开始从“自己拼基础设施”转向“在代码里声明工作流”**：这对前端团队扩展到全栈 AI 应用特别关键。
- **agent 不再只是“延长超时的 API route”**：官方明确把 durable agents、durable streams、外部事件恢复和工具调用编排都纳入同一模型。
- **对 Next.js / TypeScript 团队很友好**：应用代码本身就是 orchestrator，而不是再维护一套独立 worker 系统。

## 前端/AI 开发落地建议

### 1. 把长任务拆成业务步骤，而不是函数内部的 while loop

如果你的 AI 流程涉及：

- 多轮工具调用
- 外部 API 重试
- 用户关闭页面后继续执行
- 生成进度实时回放

那它大概率已经不适合塞进单次请求里。更稳的做法是把流程拆成 durable step，让每一步都能独立重试、持久化和观测。

### 2. 用 durable stream 设计“可离线恢复”的前端体验

Vercel 在文中专门强调，workflow 持续运行时，客户端可以断开再连，并从之前的位置恢复输出。这对前端体验很关键，尤其适合：

- 长文生成
- 代码修复
- 多步骤 agent 执行
- 文件处理和媒体处理

前端不该再默认假设“用户必须一直挂在这个页面上等结果”，而应该把恢复连接、继续查看、查看历史步骤作为默认能力。

### 3. 把 agent 的安全和审计要求前置

官方把 Workflows 定位成 security-first，并强调默认加密步骤输入、输出和 stream chunk。对团队来说，更重要的是顺手把这几件事一起做好：

- 区分纯计算步骤和副作用步骤
- 给副作用步骤保留审批或幂等设计
- 把 workflow run、tool call、用户事件串成可追踪链路

否则 durable 只是“跑更久”，不是“更可靠”。

## 注意事项

- **durable execution 不是免费午餐**：流程拆分、状态边界和错误恢复策略仍然要你自己设计。
- **别把所有逻辑都做成 step**：适合隔离、重试和审计的单元才值得 durable 化，过细会让心智负担和运行成本上升。
- **前端仍要处理进度同步和用户预期**：后端可恢复，不代表用户天然理解流程还在运行；状态文案和回放 UI 仍很重要。

## 来源链接

- Vercel Blog：A new programming model for durable execution（2026-04-16）  
  https://vercel.com/blog/a-new-programming-model-for-durable-execution
- Vercel Docs：Workflow  
  https://vercel.com/docs/workflow

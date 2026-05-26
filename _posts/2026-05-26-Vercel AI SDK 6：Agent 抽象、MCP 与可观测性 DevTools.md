# Vercel AI SDK 6：Agent 抽象、MCP 与可观测性 DevTools

## 背景

在前端/全栈做 AI 应用时，常见痛点不是“能不能调用模型”，而是：

- 复杂交互里要 **多步工具调用（tool loop）**、重试与收敛条件；
- 同一套能力要复用在 **Chat UI、后台任务、API endpoint** 等多种入口；
- 需要 **可观测性/调试**（提示词、工具调用、耗时与 token）来定位线上问题；
- 工具与外部能力逐渐通过 **MCP（Model Context Protocol）** 这类协议标准化。

Vercel 在 AI SDK 6 中把这些“工程层痛点”往 SDK 层推进：引入 `Agent` 抽象（以及 `ToolLoopAgent` 实现）、更强的工具调用与结构化输出、MCP 支持，以及用于调试与观测的 DevTools。并提供 codemod 帮助从 v5 迁移。  

## 为什么重要

- **把“多步工具调用循环”变成一等公民**：从零散的 `generateText/streamText` 组合，走向可复用、可组合的 agent 结构。
- **更像“前端工程化”的开发体验**：可观测性与调试工具不再是你自己拼一套 tracing。
- **协议化工具生态**：MCP 让工具接入更标准，减少每家都写一套“工具适配层”的碎片化成本。

## 前端/AI 开发落地建议

### 1. 把 Agent 当作“可复用的业务能力单元”

建议按业务能力拆分 agent（例如“检索 + 摘要”“工单分诊”“报表生成”），每个 agent 明确：

- 输入（prompt/结构化参数）
- 可用工具（只暴露必要工具）
- stop 条件与最大步数
- 需要人工审批的工具边界（例如涉及写库/发邮件/付费操作）

这样更容易在 UI 与后台任务之间复用，同时也更利于做权限收敛与审计。

### 2. 以“人机协作”设计不可逆工具的审批与回滚

当工具调用从“模型建议”变成“模型可执行”，工程上必须提前设计：

- 哪些工具需要审批（HITL）
- 审批的 UI/流程放在哪里（前端弹窗、后台队列、运维平台）
- 失败如何重试、如何幂等、如何回滚

### 3. 把 DevTools 纳入团队日常：从“能跑”到“可解释、可定位”

AI 相关 bug 常见于提示词与工具调用序列；建议团队把“可观测性截图/导出”作为排障材料的一部分（输入、输出、工具调用、耗时、token），否则问题只能停留在“感觉模型变笨了”。

## 注意事项

- **迁移节奏**：优先在非核心链路（内部工具、实验功能）尝试 v6，再逐步替换核心业务路径。  
- **工具权限最小化**：Agent 抽象更容易“接更多工具”，但也更容易越权；默认最小权限，并对高风险工具加审批与审计。  
- **结构化输出要做健壮性处理**：再好的 schema 也可能遇到模型偏航；对关键字段要做校验与 fallback。

## 来源链接

- AI SDK 6（Vercel Blog，2025-12-22）  
  https://vercel.com/blog/ai-sdk-6
- Migration Guide: Migrate AI SDK 5.x to 6.0（AI SDK v6 文档）  
  https://v6.ai-sdk.dev/docs/migration-guides/migration-guide-6-0
- AI SDK Agents: Overview / Building Agents（AI SDK 文档）  
  https://ai-sdk.dev/docs/agents/overview


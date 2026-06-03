# Chrome Built-in AI 最佳实践：从 Demo 到生产

## 背景

Chrome for Developers 在 2026-04-30 发布了 `Built-in AI APIs: Do and don't`。相比此前“浏览器内置 AI 能做什么”的功能介绍，这篇更像一份生产指南，重点不在 API 名称，而在怎么避免把一个看似可跑的 Demo，做成体验糟糕、延迟失控、用户不信任的线上功能。

官方文档直接指出：网页或 Web App 可以在不自托管模型的前提下提供 AI 能力，但从 Demo 走向 production-ready feature，会遇到很多技术和 UX 层面的坑。

## 为什么重要

- **浏览器内置 AI 的瓶颈不只是可用性，更是交互时机**：官方特别强调，不要等用户点下“Generate”才初始化 session，否则会出现明显冷启动延迟。
- **Prompt API 的工程细节开始影响体验**：例如 `initialPrompts` 只能在 `create()` 时设置，这意味着系统指令组织方式会直接影响首响应速度。
- **前端工程师要开始管理“本地模型生命周期”**：从能力检测到 session 复用、克隆、撤销与结果回退，这些都变成前端职责，而不是单纯丢给后端。

## 前端/AI 开发落地建议

### 1. 识别用户意图后尽早预热

官方建议很明确：一旦识别到用户正在进入 AI 流程，就应尽早初始化 session，而不是等用户最后点击提交。适合的触发点包括：

- 用户打开 AI 功能面板
- 首次点击相关输入区域
- 进入带 AI 助手的编辑页面

这样可以把模型加载和 runtime 准备，藏在用户已有的操作过程中。

### 2. 为 Prompt API 设计“基线 session + clone”模式

文档专门提出 `clone()` 的用法，这对前端很实用。一个推荐模式是：

- 创建只包含系统指令的 base session
- 每个具体任务从 base session 克隆
- 并发任务彼此隔离，避免上下文污染

这比反复创建全新 session 更稳，也更容易控制性能和上下文边界。

### 3. 把“可撤销”和“人工兜底”当成默认要求

官方在 UX 部分强调三件事：不要突然替换 UI、要让用户能撤销 AI 修改、并且始终保留人工覆盖权。对富文本、翻译、摘要、改写类场景尤其重要。工程上建议默认提供：

- 历史版本或 stepper
- 一键恢复原文
- AI 结果与原始内容对比

## 注意事项

- **不要只看首屏效果**：即时生成看起来很酷，但官方提醒，过快替换 UI 反而可能降低用户信任。
- **Prompt API 有创建时约束**：`initialPrompts` 必须在 `create()` 时提供，不能事后补。
- **不能把浏览器内置 AI 当成唯一方案**：设备性能、浏览器支持、模型可用性仍然会造成体验差异，能力检测和降级路径还是必需品。

## 来源链接

- Built-in AI APIs: Do and don't（Chrome for Developers，2026-04-30）  
  https://developer.chrome.com/docs/ai/built-in-ai-dos-donts?hl=en
- Built-in AI APIs（Chrome for Developers）  
  https://developer.chrome.com/docs/ai/built-in-apis
- Built-in AI（Chrome for Developers）  
  https://developer.chrome.com/docs/ai/built-in

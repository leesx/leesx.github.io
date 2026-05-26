# Chrome 声明式局部更新：让 HTML 流式渲染支持“乱序”

## 背景

传统的 HTML 传输与解析几乎是“自上而下、线性”的：服务器先把整段 HTML 拼好，客户端再按顺序解析，哪怕某些区域的数据已经准备好了，也很难在不写大量 JS 的情况下把它们“提前填到正确的位置”。

Chrome 团队在 **Declarative Partial Updates** 方向上提出了两组新能力：一组偏向 **HTML 层的乱序流式填充（out-of-order streaming）**，另一组偏向 **更一致、更可控的 JS 插入/流式插入 HTML API**。官方说明这些能力已可在 **Chrome 148** 通过实验性开关进行开发者测试。  

## 为什么重要

- **把“BigPipe/分块渲染”的复杂度下沉到平台层**：以前做乱序填充常要自建占位符协议 + JS 定位 + DOM 拼装；新提案让模式更声明式、更可标准化。
- **减少前端为“首屏尽快可用”写的胶水代码**：对 SSR/Streaming SSR 的应用，越少的“手写流式协议”，越容易维护与演进。
- **为更安全的 HTML 插入铺路**：浏览器可以在 API 设计层面统一 sanitizer、脚本执行策略等可选项，避免团队各自封装 `innerHTML` 带来的不一致与安全坑。

## 前端/AI 开发落地建议

### 1. 把它当成“可验证的优化路径”，先做实验，再做架构承诺

官方定位是面向开发者测试的实验性能力，建议先在可控场景（例如内容流式加载的详情页、信息流首屏骨架）做 A/B 与性能对比，再决定是否纳入长期架构。

### 2. 服务端输出策略：优先“先骨架、后填充”，并明确占位边界

如果你已经有 SSR 流式输出（或计划做），可以将页面分成：

- **同步骨架（layout + 关键导航/交互）**
- **可独立就绪的分区（评论、推荐、侧栏、卡片列表等）**

这样更容易映射到“先占位、后乱序补齐”的思路，也能减少“分区之间互相依赖导致无法乱序”的情况。

### 3. 对 AI 辅助开发团队：用“声明式协议”降低 LLM 生成代码的不可控性

当你让 LLM 帮你写“流式拼装 DOM 的胶水代码”时，最难的是验证它不会写出边缘条件 bug（选择器不稳定、时序竞态、XSS 风险、重复插入等）。偏声明式、标准化的模式更容易做静态检查与代码审查，也更便于团队沉淀最佳实践。

## 注意事项

- **实验阶段与兼容性**：目前需要在 Chrome 148 打开实验性 Web Platform Features 开关才能测试，生产环境要谨慎并准备回退。  
- **标准仍在推进**：相关 API 与语义仍在标准化过程中，命名、行为、限制都可能调整；尽量避免在核心业务里深度绑定早期细节。  
- **仍需关注安全策略**：无论是模板乱序填充还是 JS 插入 HTML，都要把 sanitizer、脚本执行策略、CSP 等作为设计的一部分，而不是事后补丁。

## 来源链接

- Declarative partial updates（Chrome for Developers，2026-05-19）  
  https://developer.chrome.com/blog/declarative-partial-updates?hl=en
- Intent to Prototype: Declarative Document Patching（blink-dev，指向 WICG 提案仓库）  
  https://groups.google.com/a/chromium.org/g/blink-dev/c/fdsXPaSLWZs/m/RvVFbvtxCAAJ


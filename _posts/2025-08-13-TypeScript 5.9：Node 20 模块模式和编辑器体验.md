# TypeScript 5.9：Node 20 模块模式和编辑器体验

TypeScript 5.9 在 2025 年 8 月发布。它不是一个“语法大改”的版本，但对前端全栈项目很实用，尤其是 Node 20 模块模式和编辑器 hover 体验。

`--module node20` 是这个版本里很值得关注的配置。现在很多前端项目并不只是浏览器代码，还包括 Next.js 服务端代码、构建脚本、CLI、Vitest、Playwright、数据库脚本。以前配置 `nodenext` 时，团队经常会被 ESM/CJS 细节困扰。`node20` 提供了一个更稳定的目标，用来描述 Node.js 20 的模块行为。

对项目配置来说，可以考虑把不同运行环境拆开：

- 浏览器应用：使用 bundler 风格的 module resolution。
- Node 脚本：使用 `module: "node20"`。
- 组件库：明确产物是 ESM、CJS，还是双产物。
- 测试环境：跟随实际 runner 的模块解析规则。

TypeScript 5.9 还改善了 DOM API 的摘要说明，并预览 expandable hovers。大型 React/TypeScript 项目里，类型经常被包装很多层。过去 hover 只能看到别名名，开发者必须跳转定义才能看清结构。可展开 hover 能减少上下文切换，对读代码和调类型很有帮助。

### 实践建议

- 不要在所有 tsconfig 里混用同一套 module 配置。
- monorepo 中为 app、packages、scripts 分别设置 tsconfig。
- 如果项目运行在 Node 20，优先验证 `module: "node20"` 是否能简化配置。
- 遇到类型 hover 太长的问题，统一 VS Code 的 hover 最大长度配置。

这个版本提醒我们：TypeScript 的价值不仅是类型检查，也包括 IDE 理解代码的能力。前端项目越大，编辑器反馈速度和类型可读性越重要。

### 来源

- [Announcing TypeScript 5.9 - TypeScript](https://devblogs.microsoft.com/typescript/announcing-typescript-5-9/)


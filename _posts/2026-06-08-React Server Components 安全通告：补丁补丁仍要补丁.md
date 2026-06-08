# React Server Components 安全通告：补丁补丁仍要补丁

## 背景

React 官方在 2025-12-11 发布了 `Denial of Service and Source Code Exposure in React Server Components`，并在 2026-01-26 更新，补充披露了新的 DoS 漏洞与最终安全版本。

这篇文章最值得前端团队警惕的地方，不是“React 又有安全公告”这么简单，而是：

- 第一轮补丁并不完整
- 后续又发现了变体漏洞
- 即使你没有显式写 `Server Function`，只要使用支持 React Server Components 的框架或构建链，也可能受到影响

## 为什么重要

- **RSC 已经不是实验室话题，而是供应链话题**：React 官方点名受影响的不只是底层包，还包括 Next、React Router、Waku、Parcel RSC、Vite RSC 插件等上层生态。也就是说，很多团队不是“主动选择了漏洞”，而是“跟着框架升级路线自然暴露了风险”。
- **补丁回合制说明这类能力要持续跟踪**：React 团队明确说明，之前发布的修复版本 `19.0.3`、`19.1.4`、`19.2.3` 仍然不完整，最终安全版本是 `19.0.4`、`19.1.5`、`19.2.4`。对工程管理来说，这意味着安全升级不能只看“有没有打过一次补丁”，而要跟踪公告是否追加。
- **影响面不只在代码执行**：这次新增披露包括高危 DoS 和中危源码泄露。前者可能导致服务挂起、CPU 飙升或 OOM，后者则可能把 `Server Function` 里的硬编码敏感信息连同源码片段一起暴露出去。

## 前端/AI 开发落地建议

### 1. 先查“是否用了 RSC 相关包”，再决定升级路径

不要只看 `react` 和 `react-dom` 版本。更应该优先确认项目里是否存在这些包：

- `react-server-dom-webpack`
- `react-server-dom-parcel`
- `react-server-dom-turbopack`

如果你是通过框架间接使用它们，也要核对锁文件和最终安装树，而不是只看业务代码里有没有 `use server`。

### 2. 把框架升级和安全回归绑在一起

React 官方说明，漏洞可能由精心构造的请求触发，造成无限循环、进程挂死或返回不该暴露的源码。前端团队升级时，建议同步做三类验证：

- SSR / RSC 路由能否正常渲染
- Server Function 的输入输出链路是否仍符合预期
- 生产构建产物里是否还存在旧版相关依赖

这类问题不适合只在本地 smoke test 后就结束。

### 3. AI 生成或自动修改服务端组件代码时，禁止硬编码敏感信息

React 官方明确指出，源码泄露问题的影响范围限于 `Server Function` 内联后的源码片段，`process.env` 这类运行时变量不在这次直接暴露范围内，但**硬编码在源码里的密钥可能会被带出**。如果你正在用 AI 辅助写服务端函数，这条约束应该写进团队规范：

- 不要把密钥、连接串、私有 token 直接写进源码
- 让 agent 生成服务端代码时默认走环境变量
- 把安全扫描纳入 CI，而不是依赖人工 review 记忆

## 注意事项

- **不是所有 React 应用都受影响**：React 官方同时说明，如果你的应用不使用服务器，或者不使用支持 RSC 的框架、打包器或插件，则不在这次影响范围内。
- **不要把托管平台缓解当成最终修复**：官方提到部分 hosting provider 提供了临时缓解，但也明确表示不应依赖它，仍应尽快升级。
- **安全版本要对准最终补丁号**：如果你已经停在 `19.0.3`、`19.1.4`、`19.2.3`，这不算完成修复。

## 来源链接

- React Blog：Denial of Service and Source Code Exposure in React Server Components（2025-12-11，2026-01-26 更新）  
  https://react.dev/blog/2025/12/11/denial-of-service-and-source-code-exposure-in-react-server-components

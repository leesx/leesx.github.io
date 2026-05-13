# Chrome 148：Immediate UI mode 让登录更丝滑

## 背景

很多网站即便已经接入了 passkeys（WebAuthn）或浏览器保存的密码，用户仍常常需要先进入“登录页”、看到表单后才能触发浏览器的凭据提示。这会带来额外跳转、更多 UI 状态、以及更高的中断率。

Chrome 148 引入了 **Immediate UI mode**：当用户在关键时刻（例如点击“登录 / 结账”按钮）发起凭据请求时，浏览器可以**立刻**展示本机可用的凭据（passkeys 与受管密码）。如果本机没有可用凭据，则**静默失败**，让网站无缝回退到原本的登录方式。

## 为什么重要

- **减少登录摩擦**：无需先跳到登录页再触发提示，登录提示“就地发生”，更像原生 App。
- **更干净的回退路径**：无凭据时不会弹出多余 UI，你可以直接展示账号密码/短信/第三方登录等。
- **统一 passkeys 与密码入口**：同一次触发里，浏览器可以提供更合适的凭据候选。

## 前端/AI 开发落地建议

### 1. 把 Immediate UI mode 绑定到“明确意图”的按钮

把触发点放在用户明确表达登录意图的交互上，例如：

- 点击“Sign in / 登录”
- 点击“Checkout / 结账”
- 点击“Continue / 继续”

按钮点击后先尝试 Immediate UI mode；如果失败（例如没有可用凭据或被拒绝），再进入你原有的登录流程。

### 2. 迁移点：从 `mediation: 'immediate'` 迁到 `uiMode: 'immediate'`

如果你之前参加过 Origin Trial，需要注意：`mediation: 'immediate'` **不再触发**该能力。现在需要在 `navigator.credentials.get()` 请求中使用 `uiMode: 'immediate'` 字段。

### 3. 用数据驱动“登录路由”（AI 工程视角）

对 AI 产品/Agent 产品来说，登录往往是第一次关键阻断。建议把登录做成可观测的“路由”：

- 首选：Immediate UI mode（本机凭据）
- 次选：已有会话/SSO（如果适用）
- 回退：账号密码/短信/第三方登录

并记录埋点（成功、失败原因、回退比例），持续优化入口与文案，而不是把所有选项平铺在一个页面里。

## 注意事项

- **需要用户手势**：建议只在用户交互后（如点击按钮）触发，避免页面加载时主动弹出。
- **回退要“无缝”**：把失败视为正常分支处理，不要让用户看到一闪而过的错误提示。
- **跨浏览器差异**：这是 Chrome 148 的新能力，务必做特性检测与回退。

## 来源链接

- Streamlined sign-in: Immediate UI mode is now available（Chrome Developers，2026-05-12）  
  https://developer.chrome.com/blog/webauthn-immediate-ui?hl=en
- Immediate UI mode for logins（Chrome Developers 文档）  
  https://developer.chrome.com/docs/identity/immediate-ui-mode

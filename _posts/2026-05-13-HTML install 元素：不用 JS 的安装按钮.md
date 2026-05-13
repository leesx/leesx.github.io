# HTML install 元素：不用 JS 的安装按钮

## 背景

Web App（PWA/类应用）的“安装入口”长期比较割裂：用户可能从地址栏、菜单或提示条进入；开发者如果想在站内提供一个确定的“安装按钮”，通常要依赖 `beforeinstallprompt` 等事件并用 JavaScript 编排流程。

Chrome/Edge 在 Chromium 中实现了新的 **`<install>` HTML 元素**：你把它放进页面里，浏览器会渲染一个“可信”的安装按钮（文本、语言、外观由浏览器控制），并把点击视为明确的用户意图，从而减少脚本编排与 UI 不一致的问题。

该能力在 Chrome/Edge **148** 起可通过 flag 测试，并提供 **Origin Trial（148–153）** 供线上验证。

## 为什么重要

- **声明式安装入口**：从“命令式 JS”转向“声明式 HTML”，减少状态管理与边缘 bug。
- **浏览器信任的 UI**：按钮由浏览器渲染，用户预期更一致，点击与弹窗的因果更清晰。
- **支持“应用目录页”**：可在同一页面为多个应用提供安装按钮，构建类似 web app store 的体验。

## 前端/AI 开发落地建议

### 1. 最小接入：安装当前站点的 web app

当页面已关联 manifest 且 manifest 含 `id` 字段时，直接使用：

```html
<install></install>
```

### 2. 目录页：安装其它 origin 的 web app

在应用卡片里放置安装按钮：

```html
<install installurl="https://awesome-app.com/"></install>
```

如果目标应用的 manifest 没有 `id`，需要提供 `manifestid`（可从 DevTools 的 Application 面板复制 Computed App ID）：

```html
<install installurl="https://awesome-app.com/" manifestid="..."></install>
```

### 3. 渐进增强：提供 fallback 内容

浏览器不支持 `<install>` 时，元素内部的内容会被展示：

```html
<install installurl="https://awesome-app.com/">
  <a href="https://awesome-app.com/">打开 Awesome App</a>
</install>
```

如需更复杂的分支逻辑，可用：

```js
if ("HTMLInstallElement" in window) {
  // 支持 <install>
}
```

### 4. 建议加上安装漏斗埋点

`<install>` 会触发成功、取消、校验错误等事件（以官方为准）。建议至少记录：

- 点击次数
- 成功安装 / 取消安装
- 校验失败原因（例如 invalidReason）

对 AI 产品尤其有价值：安装/加到桌面通常是提升留存与“回到应用”的关键动作。

## 注意事项

- **试验性能力**：需要 flag 或 Origin Trial，上线必须准备好回退策略。
- **版本窗口**：Origin Trial 覆盖 148–153，注意到期后的行为变化。
- **跨浏览器差异**：保留传统入口与引导文案，避免不支持环境里用户迷路。

## 来源链接

- Install web apps with the new HTML install element（Chrome Developers，2026-05-12）  
  https://developer.chrome.com/blog/install-element-ot?hl=en

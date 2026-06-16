# PWA Origin Migration：换域名不再丢安装用户

## 背景

Chrome for Developers 在 2026 年 6 月 3 日更新、6 月 9 日博客单独介绍了 `Seamless PWA origin migration`。这个能力会在 `Chrome 150` 中支持已安装 PWA 在同站点范围内迁移到新 origin。

这解决的是一个长期很痛的老问题：PWA 安装身份强绑定 origin。一旦你把应用从 `www.example.com/app` 挪到 `app.example.com`，或者因为重构、品牌调整、域名治理需要改地址，过去用户通常只能：

- 手动卸载旧 App
- 再去重新安装新 App

这对真实产品非常伤，尤其是已经有安装用户、桌面入口和回访习惯的 PWA。

## 为什么重要

- **PWA 终于能像原生应用一样做更平滑的“搬家”**：域名和架构调整不再等于重新拉新一遍已安装用户。
- **它给前端架构演进留下了更大余地**：过去很多团队不敢改 PWA 的部署边界，不是因为技术上做不到，而是用户迁移成本太高。
- **这是一类很典型的“平台替代产品补丁”能力**：之前要么忍着不动，要么做一堆提示页、文档、客服说明；现在浏览器开始原生处理迁移流程。

## 前端/AI 开发落地建议

### 1. 提前梳理哪些 PWA 未来可能迁移

如果你的 Web App 有这些计划，就值得提前关注：

- 从路径式应用拆到独立子域名
- 多品牌或多区域站点重组
- 历史 PWA 的 `start_url`、`id`、scope 重新整理

这种能力不是每天都用，但一旦要用，往往能避免一次高风险迁移事故。

### 2. 严格按双向握手设计迁移

官方要求迁移必须同时满足：

- 新应用 manifest 声明 `migrate_from`
- 旧 origin 在 `/.well-known/web-app-origin-association` 里显式允许迁移

这不是繁琐，而是为了避免恶意站点单方面“接管”旧 App 身份。涉及安装态能力时，安全握手必须当成设计核心。

### 3. 把用户体验策略和技术方案一起评审

官方提供了 `suggest` 和 `force` 两种迁移行为。工程上不要只看实现是否打通，还要一起评估：

- 用户是否还能继续使用旧地址
- 是否需要强制迁移
- 名称和图标是否会同步变更
- 重定向后旧 manifest 如何继续被浏览器发现

这些决定直接影响迁移期的投诉率和留存表现。

## 注意事项

- **目前只支持 same-site 迁移**：官方限制为共享同一 `eTLD+1` 的场景，不是任意跨组织换域名。
- **目标 manifest 需要显式 `id`**：否则很容易踩进“拆出重复安装应用”的老坑。
- **强制迁移有 UX 约束**：`force` 模式下不能同时修改应用名称和图标，这类变更要拆到迁移完成后再做。

## 来源链接

- Chrome for Developers：Seamless PWA origin migration: Change domains without losing users（2026-06-03，页面最后更新）  
  https://developer.chrome.com/blog/seamless-pwa-origin-migration
- Chrome for Developers：Chrome 150 beta（2026-06-03）  
  https://developer.chrome.com/blog/chrome-150-beta

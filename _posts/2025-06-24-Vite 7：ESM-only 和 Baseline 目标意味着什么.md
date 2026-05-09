# Vite 7：ESM-only 和 Baseline 目标意味着什么

Vite 7 在 2025 年 6 月发布，核心信号很明确：现代前端工具链继续向 ESM-only、更新 Node 版本和 Web Platform Baseline 靠拢。

Vite 7 要求 Node.js 20.19+ 或 22.12+，并放弃 Node 18。原因之一是 Node 18 已在 2025 年 4 月结束生命周期，另一个原因是 Vite 希望更稳定地分发 ESM-only 包，同时仍兼容部分 CommonJS 调用场景。

这件事对日常开发的影响比“升级一个版本号”更大。很多老项目的问题不在业务代码，而在工具链假设：脚手架、测试框架、CI 镜像、私有 npm 包、monorepo 脚本可能仍默认 Node 18 或 CommonJS。升级 Vite 7 前，应该先检查：

- CI 和本地 Node 版本是否一致。
- `package.json` 里的 `type`、构建脚本、测试脚本是否依赖 CJS 行为。
- 插件是否已支持 Vite 7 和新版 Rollup。
- 浏览器目标是否仍需要照顾很旧的环境。

Vite 7 的另一个重点是默认浏览器目标转向 Baseline Widely Available。Baseline 的价值是减少“我能不能用这个 Web API”的猜测，让团队用一套更清楚的标准判断 CSS、DOM、JavaScript 特性是否安全。

在前端工程管理上，可以把 Vite 7 升级当成一次工具链体检：统一 Node 版本、清理历史 polyfill、把 browserslist 或构建目标写清楚，并把插件升级纳入锁文件审查。这样比单纯跑一个 `npm update` 更稳。

### 实践建议

- 新项目优先使用 Vite 7，Node 版本直接锁到 22 LTS 或团队标准版本。
- 老项目先升级 CI 镜像和本地版本管理，再升级 Vite。
- 对公共组件库，先验证产物格式和消费者项目是否兼容 ESM。

### 来源

- [Vite 7.0 is out! - Vite](https://vite.dev/blog/announcing-vite7)
- [Baseline 2025 - web.dev](https://web.dev/baseline/2025)


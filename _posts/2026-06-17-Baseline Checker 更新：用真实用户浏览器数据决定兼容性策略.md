# Baseline Checker 更新：用真实用户浏览器数据决定兼容性策略

## 背景

web.dev 在 2026 年 6 月 8 日发布了 `Effortlessly get Baseline target data with the updated Baseline Checker tool`。这次更新看起来不像大版本发布那样吸睛，但对真正做前端落地的人很有价值。

它解决的是一个长期存在的实际问题：

**团队知道 Baseline 很重要，但真正决定“这个新特性能不能上线”时，往往缺少自己站点用户浏览器分布的直接证据。**

这次更新后，Baseline Checker 可以直接通过 Google Analytics API 获取浏览器版本数据，不再要求手动导出再上传。官方强调，数据仍然只在本地设备处理。

## 为什么重要

- **它把兼容性讨论从“感觉上能用”变成了数据驱动决策**：不是泛泛看市场份额，而是直接看你自己用户对某个 Baseline 目标的支持比例。
- **它让 Baseline 真正进入日常工程流程**：以前手动导出数据的门槛太高，很多团队只会偶尔看一次；现在流程轻得多，更适合持续使用。
- **它对渐进增强和 fallback 策略很有帮助**：前端团队终于可以更具体地回答“这个特性应该原生上线、加降级，还是继续等待”。

## 前端/AI 开发落地建议

### 1. 把 Baseline Checker 变成功能评审前的一步

遇到新 CSS、Web API 或交互能力时，可以先看自己站点用户对目标 Baseline 的覆盖情况，再决定：

- 直接采用原生能力
- 原生能力加 fallback
- 暂缓上线

这比单纯依赖全网兼容性统计更贴近真实业务。

### 2. 让 AI 编码流程引用“你的兼容性基线”

如果团队已经在用 AI 编码工具，建议把 Baseline Checker 的结果转成项目规则，例如：

- 当前默认目标 Baseline 是什么
- 哪些页面允许更激进的新特性
- 哪些场景必须补降级方案

这样 AI 生成代码时，参考的就不只是公开文档，而是你的真实用户数据。

### 3. 把兼容性策略和埋点、回滚机制绑在一起

决定采用新能力后，最好同步准备：

- 关键页面异常监控
- 新特性的使用路径埋点
- fallback 是否命中
- 回滚开关或替代方案

这样即使判断偏乐观，也能快速修正。

## 注意事项

- **它依赖 Google Analytics 数据质量**：如果 GA 采样、日期范围或流量结构本身有偏差，结论也会跟着偏。
- **Baseline 覆盖率不是唯一判断标准**：还要看业务关键路径、目标人群和 fallback 成本。
- **本地处理不等于可以忽视隐私治理**：团队仍然要明确谁能访问分析数据，以及结果如何进入工程决策。

## 来源链接

- web.dev：Effortlessly get Baseline target data with the updated Baseline Checker tool（2026-06-08）  
  https://web.dev/blog/baseline-ga-checker-update

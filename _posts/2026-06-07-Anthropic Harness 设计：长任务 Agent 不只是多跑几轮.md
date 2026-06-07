# Anthropic Harness 设计：长任务 Agent 不只是多跑几轮

## 背景

Anthropic Engineering 在 2026-03-24 发布了 `Harness design for long-running application development`。这篇文章的价值不在于“Claude 又变强了”，而在于它把一个很关键的 AI 工程事实讲透了：

**长任务 agent 的上限，往往不是模型参数，而是 harness 设计。**

文中作者从前端设计生成和长时间 автономous coding 两个方向出发，总结出一个共通结论：单 agent 连续跑很容易逐步偏航，而更稳的做法是把规划、实现、评估拆成不同角色，并用结构化产物在角色间交接上下文。

## 为什么重要

- **多轮执行不是把 prompt 拉长就行**：Anthropic 明确提到，他们最终采用了 `planner + generator + evaluator` 三个角色，而不是期待单个 agent 在几个小时里始终稳定。
- **评估器是能力放大的关键，不只是验收补丁**：文章里强调 evaluator 会按 sprint contract 的测试标准运行 Playwright、提具体 bug。对 AI 工程来说，这说明 QA 不是附属环节，而是 agent 体系的一部分。
- **上下文管理要随模型能力重新设计**：文中回顾早期 harness 依赖 context reset，而在更新模型后又去掉这层结构，改由连续会话和自动压缩承接。这提醒我们：harness 不是一次设计终身有效。

## 前端/AI 开发落地建议

### 1. 不要让一个 agent 同时负责“想、做、验”

如果任务涉及：

- 需求扩写
- 页面或接口实现
- 回归验证

建议至少显式拆成三个阶段：

- 规划阶段：只负责扩写目标、限制范围和验收标准
- 生成阶段：按 feature 或 sprint 推进实现
- 评估阶段：基于明确 contract 做检查和回归

这会比“让一个 agent 自己边写边想边验”更稳定，也更容易定位失败点。

### 2. 用 sprint contract 管 agent，而不是只管最终结果

Anthropic 这篇文章里最值得借鉴的，不是多 agent 本身，而是它让 evaluator 对着一份具体 contract 去验收。对前端团队来说，可以把 contract 理解成每一轮必须交付的最小规范，例如：

- 页面交互是否完整
- API 路由是否可达
- 边界条件是否覆盖
- 回归脚本是否通过

只要 contract 够清晰，agent 失败时你看到的就不是“这轮感觉不太对”，而是具体哪条标准没满足。

### 3. 把浏览器验证写进 harness 默认流程

文中 evaluator 会直接操作运行中的应用并通过 Playwright 找问题。这对前端项目尤其重要，因为很多 bug 根本不是静态代码层面能看出来的，比如：

- 事件绑定不生效
- 拖拽或键盘操作异常
- 路由匹配顺序错误
- 局部布局或状态回退问题

如果你的 agent 还没有真实运行页面并验证交互，它大概率只是“会写代码”，还谈不上“会交付功能”。

## 注意事项

- **多 agent 不等于越多越好**：组件越多，成本、时延和排障难度也会上升。先从最必要的 planner / evaluator 开始更稳。
- **评估器默认并不可靠**：Anthropic 也明确写到，evaluator 需要反复调 prompt 和看日志，否则它会发现问题却又轻易放过。
- **harness 要跟着模型演进一起重构**：旧模型需要的脚手架，下一代模型可能已经不需要；反过来，新能力也会让新的结构变得值得加入。

## 来源链接

- Anthropic Engineering：Harness design for long-running application development（2026-03-24）  
  https://www.anthropic.com/engineering/harness-design-long-running-apps

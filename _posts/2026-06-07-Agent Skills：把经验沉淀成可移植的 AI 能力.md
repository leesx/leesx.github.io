# Agent Skills：把经验沉淀成可移植的 AI 能力

## 背景

Anthropic Engineering 在 2025-10-16 发布了 `Equipping agents for the real world with Agent Skills`，并在 2025-12-18 补充说明，已经把 Agent Skills 发布为跨平台可移植的开放标准。它讨论的不是某个单独模型功能，而是一种更接近工程资产沉淀的思路：

**把组织里的流程经验、脚本和参考资料，打包成 agent 可以按需加载的 skill。**

这件事对 AI 工程团队很重要，因为很多 agent 失败并不是“模型不会推理”，而是它缺少具体流程知识、局部约束和可执行工具。

## 为什么重要

- **Skill 让经验不再只活在 prompt 里**：Anthropic 把 skill 定义成一个带 `SKILL.md` 的目录，里面可以包含说明、脚本和额外资料。这意味着经验可以像代码资产一样被组织、复用和审查。
- **progressive disclosure 解决了上下文浪费问题**：官方强调 agent 启动时只预载每个 skill 的 `name` 和 `description`，真正需要时再读完整 `SKILL.md` 或附加文件。这样既能扩充能力，又不会把上下文一开始就塞满。
- **Skill 天然适合和代码执行结合**：文章专门指出，skill 不只是文档容器，也可以带脚本，让 agent 在需要时直接运行确定性的代码能力。

## 前端/AI 开发落地建议

### 1. 把重复出现的“做事方法”抽成 skill

如果你的团队已经反复要求 agent 做这些事：

- 按固定博客结构写文章
- 按团队规范改 UI
- 生成发布检查清单
- 处理某类 API、表格或文档

那就不要继续靠复制 prompt。更可维护的做法是把流程说明、示例和辅助脚本放进一个 skill 目录，让 agent 只在命中场景时加载。

### 2. skill 要分层，而不是把所有内容塞进一个文件

Anthropic 明确把 skill 的核心原则定义为 progressive disclosure。对工程实践来说，很适合按这三层拆：

- 元信息层：`name`、`description`，只负责告诉 agent 什么时候该用
- 主说明层：`SKILL.md`，只保留主流程、边界和关键决策
- 扩展资料层：把低频细节、脚本说明、特定场景手册拆到独立文件

这样能减少 token 浪费，也更适合长期维护。

### 3. 用 skill 承载“确定性步骤”，把模型留给判断

文章特别强调，很多任务更适合交给传统代码执行。前端和全栈团队可以据此做一个很实用的划分：

- 让模型负责选择策略、解释上下文和拼接流程
- 让脚本负责解析文件、批量修改、表单抽取、格式校验等确定性任务

这样 skill 就不只是“更长的提示词”，而是“说明 + 代码 + 资源”的组合能力。

## 注意事项

- **skill 也有供应链风险**：Anthropic 明确提醒，只应安装可信来源的 skill；对不够可信的 skill，要先审查脚本、依赖和外部网络访问意图。
- **skill 名称和描述本身就是触发器**：如果 `name` 和 `description` 写得含糊，agent 可能根本不会在正确场景下加载它。
- **不要先拍脑袋设计一大堆 skill**：官方建议先从评估开始，观察 agent 真正缺什么，再按缺口逐步补。

## 来源链接

- Anthropic Engineering：Equipping agents for the real world with Agent Skills（2025-10-16，2025-12-18 更新）  
  https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills
- Agent Skills  
  https://agentskills.io/

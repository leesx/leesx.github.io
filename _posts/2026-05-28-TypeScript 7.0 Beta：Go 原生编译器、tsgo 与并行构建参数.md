# TypeScript 7.0 Beta：Go 原生编译器、tsgo 与并行构建参数

## 背景

TypeScript 团队在 2026-04-21 发布了 **TypeScript 7.0 Beta**。这次更新的核心不在“新增多少语法特性”，而在于一次工程底座的切换：TypeScript 的编译器与语言服务从原先的自举 TypeScript/JavaScript 实现，逐步迁移到 **Go 的原生实现**（官方称与 TS 6.0 的语义保持结构性一致），并以并行化与共享内存并发为基础带来显著的性能提升。

同时，官方为迁移期准备了“可并存”的安装与运行方式：通过 `@typescript/native-preview` 安装、用 `tsgo` 替代 `tsc`，并提供 side-by-side 方案减少生态冲突。

## 为什么重要

- **大型前端仓库的“反馈回路”会明显变短**：更快的类型检查、构建与语言服务意味着更短的 CI 时长、更顺滑的编辑器体验，直接提升工程效率。
- **并行化带来新的可调参空间**：`--checkers` / `--builders` 等开关让你可以针对本地开发机与 CI runner 做更合理的吞吐/内存权衡。
- **迁移期更需要“可控升级”**：官方明确强调可与 TS 6.0 并存运行，这对依赖 `typescript` peer dependency 的工具链（如 lint、transform、IDE 插件）尤为关键。

## 前端/AI 开发落地建议

### 1. 先用“并存”方式做基准测试，再谈升级

建议先把 TS7 Beta 当作“性能与兼容性基准工具”接入：

- 安装：`npm install -D @typescript/native-preview@beta`
- 运行：在 CI 或本地脚本中，把关键路径的 `tsc` 替换为 `tsgo` 做对比（耗时、内存、产物一致性）

目标是先回答两个问题：**能不能过**（兼容性）与 **快多少**（收益）。

### 2. 在 CI 固定并行参数，避免团队间不一致

TS7 引入并行执行，并提供：

- `--checkers`：类型检查 worker 数（默认 4）
- `--builders`：project references 并行 builder 数
- `--singleThreaded`：强制单线程（便于排查与对比）

建议在 CI 固定 `--checkers`（必要时也固定 `--builders`），并配合机器规格做调参，避免“本地快、CI 爆内存”或“团队成员结果不一致”的问题。

### 3. 预先梳理 TS6 默认值变化，降低 TS7 迁移摩擦

官方说明 TS7 兼容 TS6 的语义，但会继承 TS6 的新默认行为，并把 TS6 的部分弃用项升级为硬错误。落地上建议把迁移拆两步：

1) 先把仓库升级到 TS6 并清理弃用项  
2) 再在关键包/关键流水线中启用 TS7（或 `tsgo`）做试运行

### 4. 对工具链依赖 `typescript` 的场景，使用官方兼容包隔离风险

官方提供了 `@typescript/typescript6` 用于在 TS7 成为默认 `typescript` 包之前，继续为生态提供 TS6 API/二进制入口（并建议用 npm alias 的方式解决 peer dependency 绑定）。如果你的仓库里存在“必须 import typescript API”的工具（自研脚本、eslint 规则、codemod 等），优先按官方指引把依赖关系拆清楚再升级。

## 注意事项

- **Beta ≠ 毫无风险**：尽管官方强调稳定性与兼容性，但在你的项目上仍建议先跑全量类型检查、增量构建、以及最关键的发布流程。
- **并行提升可能带来内存峰值**：`--checkers` 与 `--builders` 叠加会放大并发度；CI 上务必先用真实仓库压测并观察 RSS。
- **程序化 API 的稳定时间线**：官方提到稳定的 programmatic API 需要在后续版本（至少 TS 7.1）才会提供，依赖内部 API 的工具要谨慎。

## 来源链接

- TypeScript：Announcing TypeScript 7.0 Beta（2026-04-21）  
  https://devblogs.microsoft.com/typescript/announcing-typescript-7-0-beta/
- VS Code 扩展：TypeScript Native Preview  
  https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-next


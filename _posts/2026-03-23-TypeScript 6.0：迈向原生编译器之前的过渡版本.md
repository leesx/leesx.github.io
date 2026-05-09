# TypeScript 6.0：迈向原生编译器之前的过渡版本

TypeScript 6.0 在 2026 年 3 月发布。这个版本的定位很特殊：它是当前 JavaScript 代码库上的最后一个主要版本，并为 TypeScript 7.0 的原生编译器做迁移准备。

对前端团队来说，最重要的不是“马上用新语法”，而是理解默认值和弃用项的变化。TypeScript 6.0 明确反映了现代 JavaScript 生态的现实：ESM 成为主流，现代浏览器和运行时基本常青，ES5、AMD、UMD、SystemJS、classic module resolution 这些历史目标越来越少见。

几个值得关注的方向：

- `target: es5` 被弃用，最低目标转向更现代的环境。
- module 默认更靠近 ESM。
- `moduleResolution node10/classic` 这类旧解析模式应迁移到 `nodenext` 或 `bundler`。
- `baseUrl`、`outFile` 等历史配置需要重新评估。
- 新增 `es2025` target/lib，并补充 Temporal 等类型。

这会影响很多老前端项目，特别是配置长期没人动的 monorepo。升级时不要只看业务代码是否报错，还要审查 tsconfig 的历史包袱。很多配置可能是几年前为了解决 webpack、babel、老浏览器问题加上的，现在已经不再需要。

### 升级建议

1. 先在 CI 中增加 `tsc --showConfig` 输出，确认真实配置。
2. 把应用、库、脚本的 tsconfig 分开迁移。
3. 对仍需要 ES5 的场景，考虑让外部编译器负责降级。
4. 尝试 `--stableTypeOrdering`，减少声明输出差异带来的迁移噪音。
5. 提前关注 TypeScript 7.0 原生预览，但不要直接压到生产关键链路。

TypeScript 6.0 的核心价值，是逼团队清理旧时代配置，为更快的类型检查和语言服务做准备。

### 来源

- [Announcing TypeScript 6.0 - TypeScript](https://devblogs.microsoft.com/typescript/announcing-typescript-6-0/)
- [Progress on TypeScript 7 - December 2025](https://devblogs.microsoft.com/typescript/progress-on-typescript-7-december-2025/)


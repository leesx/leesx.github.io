# React Compiler 1.0：自动 memo 化进入稳定阶段

2025 年 10 月，React Compiler 1.0 发布。它的目标是让 React 应用在构建时自动做 memo 化优化，减少开发者手写 `useMemo`、`useCallback`、`memo` 的心智负担。

过去 React 性能优化很容易变成经验主义：看到组件重复渲染，就加 `memo`；看到函数被重新创建，就加 `useCallback`。但这些优化不总是有收益，还会让代码变难读。React Compiler 的价值在于把一部分机械优化交给编译器，同时保留 React 的声明式写法。

不过，Compiler 稳定不等于项目可以无脑打开。它依赖代码满足 React 规则，尤其是组件和 Hooks 必须保持纯粹，不能在渲染阶段做副作用，也不能依赖不稳定的外部可变状态。换句话说，Compiler 会奖励规范代码，也会暴露历史代码里的不稳定写法。

### 建议的采用方式

1. 先升级 `eslint-plugin-react-hooks`，打开 compiler 相关 lint。
2. 在新页面、新模块或低风险包中试点。
3. 用 React DevTools 和性能录制对比，而不是凭感觉判断收益。
4. 遇到编译跳过时，先修代码纯度，不要急着加手写 memo。

Compiler 对团队协作的影响也很大。以前 code review 里经常争论“这里要不要 useCallback”，未来更应该关注数据流、组件边界、状态位置和副作用是否清晰。手写 memo 不会消失，但应该变成少数有证据的优化。

### 适合先试的项目

- React 19+ 新项目。
- 组件层级深、交互频繁的后台应用。
- 已经严格遵守 Hooks lint 的代码库。
- 配合 Vite、Next.js、Expo 等支持路径明确的项目。

### 来源

- [React Compiler v1.0 - React](https://react.dev/blog/2025/10/07/react-compiler-1)
- [React Conf 2025 Recap - React](https://react.dev/blog/2025/10/16/react-conf-2025-recap)


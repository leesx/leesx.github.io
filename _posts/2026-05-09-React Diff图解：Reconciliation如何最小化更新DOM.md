# React Diff 图解：Reconciliation 如何最小化更新 DOM

![React Diff 图解](/img/react-diff-reconciliation-diagram.png)

React 的 Diff，也叫 Reconciliation，是 React 在状态变化后比较新旧 Virtual DOM，并计算最小 DOM 更新操作的过程。

它解决的问题很直接：组件状态变化后，UI 结构可能发生变化，但真实 DOM 操作成本较高。React 不希望每次都重建整棵 DOM 树，而是先在内存中比较新旧树，找出哪些节点可以复用、哪些节点要新增、移动、删除或更新属性，最后在 Commit 阶段一次性应用到真实 DOM。

### 1. Diff 的核心目标

React Diff 的目标不是找出理论上最完美的树编辑距离，而是在前端场景中用足够快的启发式算法完成更新。

核心原则：

- 不同类型的节点，直接替换。
- 相同类型的节点，复用节点并更新 props。
- 同层子节点通过 `key` 辅助匹配。
- Diff 只在同一层级比较，不做跨层级深度搜索。
- 最终把变化标记成 Effect，再交给 Commit 阶段执行。

这样做的收益是复杂度可控，适合频繁交互的 Web 应用。

### 2. 同层比较

React 不会随意把一棵树中的节点拿去和另一层的节点比较。它默认只比较同一层级的节点。

例如：

```text
旧树：ul -> li A, li B, li C
新树：ul -> li B, li A, li D
```

React 会先确认根节点 `ul` 类型相同，因此可以复用 `ul`。接着进入 `ul` 的子节点列表，对 `li` 节点做同层比较。

这个策略让 Diff 更快，但也要求开发者在列表渲染时提供稳定的 `key`，否则 React 很难判断节点身份。

### 3. 类型不同，直接替换

如果新旧节点类型不同，React 会认为它们代表不同 UI 结构，直接删除旧节点并创建新节点。

例如：

```text
旧：<div />
新：<section />
```

即使它们内部内容相似，React 也不会尝试复用这个节点。因为类型变了，节点语义、属性和生命周期都可能不同。

对应操作通常是：

- 删除旧节点：`Deletion`
- 插入新节点：`Placement`

### 4. 类型相同，复用节点并更新 props

如果节点类型相同，React 会尽量复用已有节点，只更新变化的属性。

例如：

```jsx
<button className="primary">保存</button>
<button className="danger">删除</button>
```

两个节点都是 `button`，React 会复用原来的 DOM 节点，只更新 `className` 和文本内容。这比删除后重建更高效，也能减少不必要的布局和绘制。

对应操作通常是：

- 复用节点：`Reuse`
- 更新属性：`Update props`

### 5. 子节点通过 key 匹配

列表 Diff 中最关键的是 `key`。

图中的例子是：

```text
旧列表：A(key=a), B(key=b), C(key=c)
新列表：B(key=b), A(key=a), D(key=d)
```

React 会根据 `key` 判断节点身份：

- `B(key=b)`：旧列表中存在，复用并移动到前面。
- `A(key=a)`：旧列表中存在，复用并移动到后面。
- `C(key=c)`：新列表中不存在，删除。
- `D(key=d)`：旧列表中不存在，新增。

因此最终结果不是简单地重建三个节点，而是尽量复用旧节点，只执行必要的移动、删除和新增。

### 6. 为什么不要用 index 做 key

`key` 代表的是节点身份，不是节点位置。

如果用数组下标作为 `key`：

```jsx
items.map((item, index) => <li key={index}>{item.name}</li>)
```

当列表发生插入、删除、排序时，下标会变化，React 可能误以为某个位置的节点没有变，导致状态错位、输入框内容错乱或动画异常。

更好的方式是使用稳定业务 ID：

```jsx
items.map((item) => <li key={item.id}>{item.name}</li>)
```

只有列表永远不排序、不插入、不删除时，index 才相对安全。

### 7. Effect Tag：把变化标记出来

Diff 的结果不会立刻修改真实 DOM，而是给 Fiber 节点打上操作标记。

常见标记包括：

- `Placement`：新增或移动节点。
- `Deletion`：删除节点。
- `Update`：更新 props、文本或事件。
- `Move`：列表重排时移动节点。
- `Reuse`：节点可复用。

这些标记会在后续 Commit 阶段被统一执行。

### 8. Fiber 链表结构

React 内部不是只用普通树结构描述节点，还会通过 Fiber 节点维护多种关系：

- `child`：第一个子节点。
- `sibling`：兄弟节点。
- `return`：父节点。
- `alternate`：对应的旧 Fiber。
- `flags`：本次更新的副作用标记。

更新时，React 会基于当前 Fiber 树构建新的 `workInProgress` 树。新旧 Fiber 之间通过 `alternate` 关联，方便比较和复用。

可以简化理解为：

```text
current Fiber tree
        |
        | 通过 alternate 对比
        v
workInProgress Fiber tree
```

### 9. Commit 阶段：最小化更新真实 DOM

Render 阶段完成 Diff 和标记后，Commit 阶段才会真正操作 DOM。

以图中的列表为例：

- 将 `B` 移动到索引 0。
- 将 `A` 移动到索引 1。
- 删除旧节点 `C`。
- 插入新节点 `D` 到索引 2。

最终真实 DOM 变成：

```html
<ul>
  <li key="b">B</li>
  <li key="a">A</li>
  <li key="d">D</li>
</ul>
```

页面只执行必要操作，不会整棵树重建。

### 10. 开发中的最佳实践

- 列表必须使用稳定、唯一的 `key`。
- 不要在会排序、插入、删除的列表中使用 index 作为 key。
- 组件类型不要频繁在同一个位置切换，否则会导致卸载和重建。
- 状态尽量放在稳定组件边界内，避免重排时状态丢失。
- 大列表优先考虑虚拟滚动，Diff 不是无限性能保证。
- 性能问题先用 React DevTools Profiler 定位，再决定是否优化。

### 总结

React Diff 的本质是一次“新旧 UI 描述的对比”：

```text
状态变化
-> 生成新的 Virtual DOM
-> 基于 Fiber 比较新旧节点
-> 根据 type 和 key 判断复用、移动、删除、新增
-> 给 Fiber 打 Effect 标记
-> Commit 阶段更新真实 DOM
```

理解 Diff 之后，再看 React 的 `key`、Fiber、Render/Commit 阶段和性能优化，就会清楚很多：React 并不是魔法，它是在用一套可预测的规则，把状态变化转换成尽量少的 DOM 操作。


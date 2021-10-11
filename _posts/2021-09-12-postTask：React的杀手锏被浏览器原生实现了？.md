### postTask：React的杀手锏被浏览器原生实现了？

[postTask：React的杀手锏被浏览器原生实现了？](https://mp.weixin.qq.com/s/ve0Ygl0ofBRfct3UjfQIsA)

> 根据任务优先级将任务拆解，分配执行时间的技术，就是优先级调度。
> scheduler.postTask是Chrome实现的优先级调度API


### 之前是如何实现优先级调度的

在scheduler.postTask出现之前，通常使用浏览器提供的会在不同阶段调用的API模拟优先级调度，比如：

- requestAnimationFrame(简称 rAF) 一般用来处理动画，会在浏览器渲染前触发
- requestIdleCallback(简称rIC) 在每一帧没有其他任务的空闲时间调用
- setTimeout、postMessage、MessageChannel在渲染之间触发

React 使用MessageChannel实现优先级调度，setTimeout作为降级方案
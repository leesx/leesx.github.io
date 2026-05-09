# Chrome Built-in AI：浏览器内置模型给前端带来的新边界

Chrome 的 Built-in AI APIs 在 2025 年持续更新。它的方向很有意思：让一部分 AI 能力直接在浏览器里运行，通过 Gemini Nano 等本地模型完成摘要、改写、语言检测、翻译或 prompt 处理。

对前端开发来说，这不是简单地把云端 API 换成本地 API，而是重新划分“客户端能做什么”。如果模型在浏览器里可用，一些轻量 AI 功能可以减少网络延迟、降低服务端成本，并改善隐私体验。例如：

- 本地草稿改写。
- 表单输入建议。
- 离线或弱网摘要。
- 用户私密文本的客户端处理。
- 浏览器扩展里的智能分类。

但它也带来新的工程问题。内置 AI API 的可用性不是所有浏览器一致，模型下载、设备性能、权限、语言支持都可能影响体验。所以不能把它当成唯一后端。更稳的设计是能力检测加降级：

```js
if ("LanguageModel" in self) {
  // 优先使用浏览器内置能力
} else {
  // 降级到服务端模型
}
```

在产品层面，也要避免让用户误以为结果一定来自云端或一定不会上传。AI 功能的隐私说明、失败提示和重试策略都应该明确。

### 适合前端先试的功能

- 非关键路径的写作辅助。
- 本地搜索前的 query 改写。
- 大段文本的快速摘要预览。
- 浏览器扩展或 PWA 的离线辅助。

Built-in AI 的长期意义，是让前端从“只负责调用后端 AI”变成“在客户端、边缘和服务端之间调度 AI 能力”。这要求前端工程师理解更多运行时差异。

### 来源

- [Built-in AI APIs - Chrome for Developers](https://developer.chrome.com/docs/ai/built-in-apis)
- [Built-in AI - Chrome for Developers](https://developer.chrome.com/docs/ai/built-in)


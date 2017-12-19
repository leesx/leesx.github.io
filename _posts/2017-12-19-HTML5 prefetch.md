#### HTML5 prefetch

[文章来自](http://www.jianshu.com/p/7f58ddfc1392)

相关阅读MDN [通过rel="preload"进行内容预加载](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Preloading_content)

- prefetch 即预加载，在用户需要前我们就将所需的资源加载完毕。

有了浏览器缓存，为何还需要预加载？

用户可能是第一次访问网站，此时还无缓存
用户可能清空了缓存
缓存可能已经过期，资源将重新加载
用户访问的缓存文件可能不是最新的，需要重新加载
Chrome 的预加载技术

现在的 chrome 聪明到根据你的浏览记录，预测到你可能访问或搜索哪些网站，在你打开网站之前就加载好了一些资源了。
举个栗子，当你在搜索框输入 "amaz" 时，它猜测到你可能要访问 amazon.com，可能就帮你加载了这个网站的一些资源。
如果这个预测算法精准的话，就能大大地提高用户的浏览体验了。

#### DNS prefetch

我们知道，当我们访问一个网站如 www.amazon.com 时，需要将这个域名先转化为对应的 IP 地址，这是一个非常耗时的过程。

DNS prefetch 分析这个页面需要的资源所在的域名，浏览器空闲时提前将这些域名转化为 IP 地址，真正请求资源时就避免了上述这个过程的时间。

```
<meta http-equiv='x-dns-prefetch-control' content='on'>
<link rel='dns-prefetch' href='http://g-ecx.images-amazon.com'>
<link rel='dns-prefetch' href='http://z-ecx.images-amazon.com'>
<link rel='dns-prefetch' href='http://ecx.images-amazon.com'>
<link rel='dns-prefetch' href='http://completion.amazon.com'>
<link rel='dns-prefetch' href='http://fls-na.amazon.com'>
```
应用场景1：我们的资源存在在不同的 CDN 中，那提前声明好这些资源的域名，就可以节省请求发生时产生的域名解析的时间。
应用场景2：如果我们知道用户接下来的操作一定会发起一起资源的请求，那就可以将这个资源进行 DNS-Prefetch，加强用户体验。

- Resource prefetch

在 Chrome 下，我们可以用 link标签声明特定文件的预加载：

<link rel='subresource' href='critical.js'>
<link rel='subresource' href='main.css'>

<link rel='prefetch' href='secondary.js'>
在 Firefox 中或用 meta 标签声明：

<meta http-equiv="Link" content="<critical.js>; rel=prefetch">
rel='subresource' 表示当前页面必须加载的资源，应该放到页面最顶端先加载，有最高的优先级。

rel='prefetch' 表示当 subresource 所有资源都加载完后，开始预加载这里指定的资源，有最低的优先级。

注意：只有可缓存的资源才进行预加载，否则浪费资源！

- Pre render

前面说到的预解析DNS、预加载资源已经够强悍了有木有，可还有更厉害的预渲染（Pre-rendering）！

预渲染意味着我们提前加载好用户即将访问的下一个页面，否则进行预渲染这个页面将浪费资源，慎用！

<link rel='prerender' href='http://www.pagetoprerender.com'>
rel='prerender' 表示浏览器会帮我们渲染但隐藏指定的页面，一旦我们访问这个页面，则秒开了！

在 Firefox 中或用 rel='next' 来声明

<link rel="next" href="http://www.pagetoprerender.com">
不是所有的资源都可以预加载

当资源为以下列表中的资源时，将阻止预渲染操作：

URL 中包含下载资源
页面中包含音频、视频
POST、PUT 和 DELETE 操作的 ajax 请求
HTTP 认证(Authentication)
HTTPS 页面
含恶意软件的页面
弹窗页面
占用资源很多的页面
打开了 chrome developer tools 开发工具
手动触发预渲染操作

在 head 中强势插入 link[rel='prerender'] 即可：

```
var hint =document.createElement("link")
hint.setAttribute(“rel”,”prerender”)
hint.setAttribute(“href”,”next-page.html”)
document.getElementsByTagName(“head”)[0].appendChild(hint)
```
### 兼容性

这么好用的特性，当然要考虑各浏览器的兼容程度了(哭：

IE9 支持 DNS pre-fetching 但管它叫 prefetch。

IE10+ 中 dns-prefetch 和 prefetch 是等价的。

其他方面的测试，目前还没有很好的方案，暂且只能通过查看浏览器是否缓存来测试。

在 Chrome 中打开了 chrome developer tools 开发工具会阻止页面的预渲染，所以我们看不到这个过程，但可以在 chrome://cache/ 或 chrome://net-internals/#prerender 中查看。

Firefox 可以在 about:cache 中查看。

### 警告

这些特定还是实验性质的，将来可能改变。


著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

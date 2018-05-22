

> 特别声明：本文转载[@小影](//c7sky.com/)的译文《[使用 Intersection Observer 实现图片延迟加载](//c7sky.com/lazy-loading-images-using-intersection-observer.html)》一文。

如果你还没有机会看过，@Paul Lewis 发布了一个精彩的视频系列，演示了如何构建一个媒体播放器以及渐进式 Web 应用（Progressive Web App）的一些重要功能。在 [YouTube 上的这一系列视频](//www.youtube.com/playlist?list=PLNYkxOF6rcIBykcJ7bvTpqU7vt-oey72J) 包括了他在建立网站时的各个部分。观看开发者展示他们工作的视频绝对是我最喜欢的学习新事物的方式！

![](/sites/default/files/blogs/2018/1805/paul-lewis-intersection-observer.png)

当他在演示他的代码时，我注意到他创建了一个很棒的小型辅助库来延迟加载图片。我一直在寻找方法来改进我的网站性能，所以这无疑引起了我的兴趣。

## 延迟加载有什么大不了的？

延迟加载图片的目的在于，在发出对应的网络请求之前你要一直等到用户滚动页面让图片进入可见区域。如果你的网页包括了多张图片，但你只在每张图片滚动到视区时才加载，你将最终会节省带宽并确保你的网页加载的更快。

为了让你在实际中了解这一点，让我们想象下面这个页面，他有三张图片。

![](/sites/default/files/blogs/2018/1805/intersection-observer.png)

如果用户访问页面时仅看到了第一张图，我们并不想加载网页底部的比萨图片，除非用户向下滚动直到图片真正进入视区。如果我们延迟加载图片，这意味着用户只在他们需要时下载他们需要的东西，这让你的网页更加精简。

对于更有经验的开发者，你也许很熟悉延迟加载图片，毕竟这个概念已经有了一段时间。那么有什么新东西呢？！现在有很多延迟加载库可以完成这项工作。我在这个博客上曾经写过一篇（[很多年前](//deanhume.com/Home/BlogPost/lazy-loading-images-with-jquery/22)）。问题在于，几乎所有这些库都是利用滚动事件或者使用定时器来检查一个元素是否在视区内。这个方法的问题是它强制浏览器重新布局整个页面，而且在特定条件下会导致你的网站卡顿（jank）。我们可以做的更好！

## 救星 Intersection Observer！

这就是 Intersection Observer 的用武之地。Intersection Observer 内置于大多数现代浏览器，可以让你知道一个被观察元素何时进入或离开了浏览器视口。这使得它很理想，因为它可以异步传输数据，并且不会影响主线程，这让它成为一个提供反馈的有效手段。

在 [@Paul 的示例](//github.com/googlechrome/sample-media-pwa)中，它展示了如何使用 Intersection Observer 来实现当图片进入视口时延迟加载。我使用了他的初始代码，并稍作修改让它更容易理解。在这篇文章中，我将过一遍 Intersection Observer 的基础知识并向你展示你该如何用一个超级高效的方法来延迟加载图片。

## 入门

想象一个有三张图片的基本的 HTML 页面，就像上面那个一样，在这个网页上，你将有如下代码的图片元素：

```
<img class="js-lazy-image" data-src="burger.png">

```

你也许注意到上面的代码中，图片文件没有 `src` 属性么。这是因为它使用了称为 `data-src` 的 `data` 属性来指向图片源。我们将使用这来加载图片，当它们进入视口时。你也许还注意到图片元素还有一个称为 `js-lazy-image` 的 `class` —— 我们将很快在 JavaScript 代码中用它来确定我们想延迟加载的元素。

接下来，我们需要创建延迟加载页面中图片的代码。在我们的 JavaScript 文件中（需要在页面中引用），我们需要创建一个新的 Intersection Observer。

```
// Get all of the images that are marked up to lazy load
const images = document.querySelectorAll('.js-lazy-image');
const config = {
    // If the image gets within 50px in the Y axis, start the download.
    rootMargin: '50px 0px',
    threshold: 0.01
};

// The observer for the images on the page
let observer = new IntersectionObserver(onIntersection, config);
images.forEach(image => {
    observer.observe(image);
});

```

上面的示例中看起来像是一堆代码，让我们一步一步来分解。首先，我选择了页面中所有包含 `js-lazy-image` 类的图片。然后我创建了一个新的 `IntersectionObserver`，并用它来观察所有我们已经选择的带有 `js-lazy-image` 类的图片。使用 `IntersectionObserver` 的默认选项，你的回调会在元素部分进入视区和完全离开视口时被调用。在这个例子中，我会传递一些额外的配置选项给 `IntersectionObserver`。使用 `rootMargin` 允许你指定根元素的 `margin` 值，让你可以扩展或者缩减 `intersections` 的使用区域。我们想确保如果图片进入了 `Y` 轴的 `50px` 内，我们将开始下载。

现在我们已经创建了一个 Intersection Observer 并且正在观察页面上的图片，我们现在来了解 `intersection` 事件，它将在元素进入视区时触发。

```
function onIntersection(entries) {
    // Loop through the entries
    entries.forEach(entry => {
        // Are we in viewport?
        if (entry.intersectionRatio > 0) {

            // Stop watching and load the image
            observer.unobserve(entry.target);
            preloadImage(entry.target);
        }
    });
}

```

在上面的代码中，每当我们正在观察的元素进入到用户视口内，`onIntersection` 函数将被触发。此时，我们可以循环我们所观察的图片并确定哪一个在视口中。如果当前元素处在交叉比例（intersection ratio）之中，我们就知道这图片在用户视口之中并且我们可以加载它了。一旦图片加载完毕，我们不在需要观察它，使用 `unobserve()` 将它从 Intersection Observer 的观察列表中移除。

就是这样！一旦用户滚动并且图片进入视区，对应的图片将被加载。这个代码最棒的地方在于 Intersection Observer 比 Barry White 更加流畅。我试图让上面的代码尽可能的简练，但是如果你想查看完整版本，我也创建了一个 [Github 仓库](//github.com/deanhume/lazy-observer-load) 和一个在线 [实例](//deanhume.github.io/lazy-observer-load/)。

## 浏览器支持

此时此刻，你也许想知道关于这项特性的浏览器支持情况。Intersection Observer 现在已被 [Edge、Firefox、Chrome 和 Opera](//caniuse.com/#feat=intersectionobserver) 支持，这是一个好消息。

然而，为了确保我们的代码不会在不支持它的浏览器中造成破坏，我们可以使用特性检测来确定我们应该如何来加载图片。让我们看看下面的代码。

```
// If we don't have support for intersection observer, load the images immediately
if (!('IntersectionObserver' in window)) {
    Array.from(images).forEach(image => preloadImage(image));
} else {
    // It is supported, load the images
    observer = new IntersectionObserver(onIntersection, config);
    images.forEach(image => {

        observer.observe(image);
    });
}

```

在上面的代码中，我们检查了 `IntersectionObserver` 是否在当前浏览器中可用，如果不可用我们就立即加载这些图片，否则使用我们的默认行为。

如果你真的喜欢 Intersection Observer API 的易用性并想使用 polyfill 来替代。WICG 创建了一个放在了他们的 [Github 仓库](//github.com/WICG/IntersectionObserver/tree/gh-pages/polyfill)。唯一的缺点是你无法获取到原生实现给你带来的性能优势。

你甚至可以更进一步，像 [@Robin Osborne](//www.robinosborne.co.uk/2016/05/16/lazy-loading-images-dont-rely-on-javascript/#a-no-JavaScript) 建议的那样，为不启用 JavaScript 的用户添加支持。

## 总结

在这篇文章汇总，我们使用 Intersection Observer 来延迟加载图片，但你可以用它来做的更多。它可以用于判断某人是否正在查看广告，或者甚至是 `iframe` 中的元素是否在视区中。易于理解的 API 使它获得了更多的可能性。

如果你想学习更多关于 Intersection Observer 的信息，我推荐阅读 Google Developers 网站上 [这篇内容丰富的文章](//developers.google.com/web/updates/2016/04/intersectionobserver)。我也强烈推荐观看 Youtube 上 [@Paul Lewis 的视频系列](//www.youtube.com/watch?v=ncYQkOrKTaI&list=PLNYkxOF6rcIBykcJ7bvTpqU7vt-oey72J&index=7)，它包含了许多很棒的技巧，你绝对会学到些东西。


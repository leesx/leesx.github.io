
[`overscroll-behavior`](//wicg.github.io/overscroll-behavior/)是CSS的新属性，允许开发者覆盖默认的浏览器滚动行为。CSS用来控制浏览器的滚动行为，大家可能熟悉的是CSS的`overflow`属性。而对于`overscroll-behavior`属性应该还是很陌生。那么今天我们就来简单的了解这个属性。

[CSS Scroll Snap Points新规范草案](//drafts.csswg.org/css-snappoints/)提出了几个新的CSS属性，主要为触控以及输入设备的用户提供一个流式、精确的滚动体验。

> 如果你从未接触过有关于CSS Scroll Snap Points相关的知识，[可以点击这里进行了解](//www.w3cplus.com/blog/tags/581.html)。

虽然CSS Scroll Snap Points规范中提供的属性，能帮助我们改善触控以及输入设备的用户体验，但对于设备的下拉刷新的行为，还不能很好的进行控制。那么CSS的`overscroll-behavior`这个属性就可以帮助我们修改浏览器的默认行为。

`overscroll-behavior`属性允许用户在到达内容的顶部或底部时覆盖浏览器的默认行为 —— **溢出滚动行为**。包括禁用移动端的下拉刷新（`pull-to-refresh`）、删除滚动的辉光（Overscroll Glow）和回弹效果Rubberbanding等。

## 背景

在了解`overscroll-behavior`属性之前，有几个概念我们需要先进行了解，这样能更好的帮助我们更好的理解要介绍的`overscroll-behavior`属性。

### 滚动边界和滚动链接

滚动是与页面交互的最基本的方式之一，但由于浏览器的怪异的默认行为，某些用户体验模式很难处理。比如我们常见的APP中的抽屉式导航，有大量的条目，用户或能不得不滚动浏览器。当它们到达底部时，溢出容器停止滚动，因为没有更多的内容可以使用。换句话说，我们期望的效果是：**滚动到底部时，滚动停止，因为我们到达了“滚动边界”(Scroll Boundary)**。但是事实上，在Web页面中**滚动并不会停止，而是继续滚动抽屉后面的内容**。比如下图这样的效果：

![CSS overscroll-behavior](https://www.w3cplus.com/sites/default/files/blogs/2017/1711/overscroll-behavior-1.gif)

对于上图这样的行为，我们称之为**滚动链接（Scroll Chaining）**。当滚动内容时是浏览器的一种默认行为。通常情况下，默认情况很好，但有时也不可取，甚至出乎意料。某些应用程序可能希望用户在遇到滚动边界时提供不同的用户体验。

### 下接刷新 `pull-to-refresh`

`pull-to-refresh`是由Facebook和Twitter等移动应用程序推广的一种直观的手势。而这种用户交互行为在移动端中经常使用的一个用户行为。而下接刷新又有**自定义下拉刷新**和**原生下拉刷新**两种。对于这两种下拉刷新又有何区别呢？我们用两张图来阐述，这样将会更清晰，也更易帮助我们理解两者之间的区别：

**自定义下拉刷新**：

![CSS overscroll-behavior](https://www.w3cplus.com/sites/default/files/blogs/2017/1711/overscroll-behavior-2.gif)

**原生下拉刷新**：

![CSS overscroll-behavior](https://www.w3cplus.com/sites/default/files/blogs/2017/1711/overscroll-behavior-3.gif)

很明显看出两者之间的区别，原生下拉刷新会刷新整个页面，正因为如此，很多时候使用的下拉刷新时，我们需要**禁用原生的`pull-to-refresh`行为**。

以前我们要实现这种效果，我们想出各种方式来处理滚动，最常见的是通过JavaScript，添加`touch`事件，阻止滚动或者将整个页面放在一个宽度为`100vw`，高度为`100vh`的`div`容器中，防止页面溢出。这些方式虽然能实现我们想要的效果，但滚动性能却不怎么的，直接影响了用户体验。

而CSS的`overscroll-behavior`属性却能很轻易的帮助我们实现所要的需求，又能给用户提供很好的体验。

## `overscroll-behavior`的介绍

`overscroll-behavior`属性可以控制一个容器或页面`body`容器滚动时发生的默认行为。可以使用这个属性取消滚动链接、禁用、自定义下拉刷新，禁用在iOS上的回弹效果等。而且使用`overscroll-behavior`不会像前面提到其他方案一样对页面有性能影响。

`overscroll-behavior`属性有三个值：

*   `auto`：其默认值。元素（容器）的滚动会传播给其祖先元素。有点类似JavaScript中的冒泡行为一样
*   `contain`：阻止滚动链接。滚动行为不会传播给其祖先元素，但会影响节点内的局部显示。例如，Android上的光辉效果或iOS上的回弹效果。当用户触摸滚动边界时会通知用户。注意，**`overscroll-behavior:contain`在`html`元素上使用，可以阻止导航滚动操作**
*   `none`：和`contain`一样，但它也可以防止节点本身的滚动效果

> `overscroll-behavior`属性是`overscroll-behavior-x`和`overscroll-behavior-y`的简写，如果你只想控制其中一个方向的滚动行为，可以使用其中的某一个属性。

接下来，我们通过一些实例来看看`overscroll-behavior`属性的使用场景和效果。

## `overscroll-behavior`的实例

### 阻止固定元素的滚动传播

先来看一个这样的使用场景，我们有一个聊天窗口固定在页面的底部，这个聊天窗口是一个独立的组件，它与背后的内容是分离的。然而，由于滚动链接，当用户点击聊天窗口中的最后一条聊天消息时，背景的内容就会开始滚动。如下图这样的一个效果：

![CSS overscroll-behavior](https://www.w3cplus.com/sites/default/files/blogs/2017/1711/overscroll-behavior-4.gif)

要改变这样的滚动行为，我们可以在聊天窗口的元素中使用`overscroll-behavior:contain`来改变：

```
#chat .msgs {
    overflow: auto;
    overscroll-behavior: contain;
    height: 300px;
}

```

这样做的本质就是聊天窗口和页面之间创建了一个隔离。最终的效果就是，当用户触摸到聊天窗口顶部或底部时，页面将保持不变。也正如前面所说的，窗口的滚动行为不会传播给其祖先元素。

完整的Demo效果[可以点击这里](//ebidel.github.io/demos/chatbox.html)，如果你对源码感兴趣，可以[点击这里查看源码](//github.com/ebidel/demos/blob/master/chatbox.html)。

除了上述场景之外，Modal弹框是非常常见的一个场景，是不是和我有同一样的感觉，在Modal弹框中滚动时，会出现这样的效果：

![CSS overscroll-behavior](https://www.w3cplus.com/sites/default/files/blogs/2017/1711/overscroll-behavior-5.gif)

同样的解决方案，使用`overscroll-behavior:contain`就是达到我们想要的效果，当Modal弹框滚动时，并不会影响`body`的行为：

![CSS overscroll-behavior](https://www.w3cplus.com/sites/default/files/blogs/2017/1711/overscroll-behavior-6.gif)

### 禁用下拉刷新`pull-to-refresh`

禁用原生的下拉刷新效果，只需要在`html`或`body`元素中添加：

```
body {
    overscroll-behavior-y: contain;
}

```

当我们阻止了原生的下拉刷新后，就可以实现自定义的下拉刷新。否则就会出现两个下拉刷新：

未使用`overscroll-behavior`之前的效果：

![CSS overscroll-behavior](https://www.w3cplus.com/sites/default/files/blogs/2017/1711/overscroll-behavior-7.gif)

使用`overscroll-behavior`之后的效果：

![CSS overscroll-behavior](https://www.w3cplus.com/sites/default/files/blogs/2017/1711/overscroll-behavior-8.gif)

[整个示例的代码](//github.com/ebidel/demos/blob/master/chatbox.html)，下面是示例代码片段：

```
<style>
    body.refreshing #inbox {
        filter: blur(1px);
        touch-action: none; /* prevent scrolling */
    }
    body.refreshing .refresher {
        transform: translate3d(0,150%,0) scale(1);
        z-index: 1;
    }
    .refresher {
        --refresh-width: 55px;
        pointer-events: none;
        width: var(--refresh-width);
        height: var(--refresh-width);
        border-radius: 50%; 
        position: absolute;
        transition: all 300ms cubic-bezier(0,0,0.2,1);
        will-change: transform, opacity;
        ...
    }
</style>

<div class="refresher">
<div class="loading-bar"></div>
<div class="loading-bar"></div>
<div class="loading-bar"></div>
<div class="loading-bar"></div>
</div>

<section id="inbox"><!-- msgs --></section>

<script>
    let _startY;
    const inbox = document.querySelector('#inbox');

    inbox.addEventListener('touchstart', e => {
        _startY = e.touches[0].pageY;
    }, {passive: true});

    inbox.addEventListener('touchmove', e => {
        const y = e.touches[0].pageY;
        // Activate custom pull-to-refresh effects when at the top of the container
        // and user is scrolling up.
        if (document.scrollingElement.scrollTop === 0 && y > _startY &&
            !document.body.classList.contains('refreshing')) {
        // refresh inbox.
        }
    }, {passive: true});
</script>

```

### 禁用炫光和回弹效果

如果将`overscroll-behavior`属性的值设置为`none`时，可以禁用默认的滚动边界效果。

```
body {
    /* 禁用默认的下拉刷新和边界效果,但是依然可以进行滑动导航 */
    overscroll-behavior-y: none;
}

```

未使用`overscroll-behavior`之前的效果：

![CSS overscroll-behavior](https://www.w3cplus.com/sites/default/files/blogs/2017/1711/overscroll-behavior-9.gif)

使用`overscroll-behavior`之后的效果：

![CSS overscroll-behavior](https://www.w3cplus.com/sites/default/files/blogs/2017/1711/overscroll-behavior-10.gif)

如果想禁用左右滑动的手势导航，可以使用 `overscroll-behavior-x: none`。

## 总结

这篇文章通过几个简单的示例，演示了CSS的`overscroll-behavior`属性的特性。简单的讲，这个属性就是帮助我们能更好的控制元素滚动的默认行为。在实际的项目中借助于这个特性能帮助我们更好的改善用户体验。这样一来，如果[CSS Scroll Snap Points新规范草案](//drafts.csswg.org/css-snappoints/)提供的CSS属性也能像该属性一样进入实质的使用阶段的话，那么我们控制浏览器默认的滚动行为就更强了。

如果你对浏览器默认的滚动行为有更深的了解，或者有更多的经验，欢迎与我们一起分享。

> **特别声明：文中使用的图片和示例代码都来源于《[Take control of your scroll: customizing pull-to-refresh and overflow effects](https://developers.google.com/web/updates/2017/11/overscroll-behavior)》一文。**

转自：[https://www.w3cplus.com/css/overscroll-behavior.html](//www.w3cplus.com/css/overscroll-behavior.html)

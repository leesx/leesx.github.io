---
layout:     post
title:      H5拖拽与拖放简介
subtitle:    
date:       2016-12-15
author:     leesx
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - Drag
---

> “🙉🙉🙉 ”

[参考这个](http://www.zhangxinxu.com/wordpress/2011/02/html5-drag-drop-%E6%8B%96%E6%8B%BD%E4%B8%8E%E6%8B%96%E6%94%BE%E7%AE%80%E4%BB%8B/)

```
<div class="dustbin"><br />垃<br />圾<br />箱</div>
<div class="dragbox">
    <div class="draglist" title="拖拽我" draggable="true">列表1</div>
    <div class="draglist" title="拖拽我" draggable="true">列表2</div>
    <div class="draglist" title="拖拽我" draggable="true">列表3</div>
    <div class="draglist" title="拖拽我" draggable="true">列表4</div>
    <div class="draglist" title="拖拽我" draggable="true">列表5</div>
    <div class="draglist" title="拖拽我" draggable="true">列表6</div>
</div>
<div class="dragremind"></div>
JS代码如下：

var $ = function(selector) {
    /*简单的选择器方法*/
    ...
};

var eleDustbin = $(".dustbin")[0], eleDrags = $(".draglist"), lDrags = eleDrags.length, eleRemind = $(".dragremind")[0], eleDrag = null;
for (var i=0; i<lDrags; i+=1) {
    eleDrags[i].onselectstart = function() {
        return false;
    };
    eleDrags[i].ondragstart = function(ev) {
        /*拖拽开始*/
        //拖拽效果
        ev.dataTransfer.effectAllowed = "move";
        ev.dataTransfer.setData("text", ev.target.innerHTML);
        ev.dataTransfer.setDragImage(ev.target, 0, 0);
        eleDrag = ev.target;
        return true;
    };
    eleDrags[i].ondragend = function(ev) {
        /*拖拽结束*/
        ev.dataTransfer.clearData("text");
        eleDrag = null;
        return false
    };
}
eleDustbin.ondragover = function(ev) {
    /*拖拽元素在目标元素头上移动的时候*/
    ev.preventDefault();
    return true;
};

eleDustbin.ondragenter = function(ev) {
    /*拖拽元素进入目标元素头上的时候*/
    this.style.color = "#ffffff";
    return true;
};
eleDustbin.ondrop = function(ev) {
    /*拖拽元素进入目标元素头上，同时鼠标松开的时候*/
    if (eleDrag) {
        eleRemind.innerHTML = '<strong>"' + eleDrag.innerHTML + '"</strong>被扔进了垃圾箱';
        eleDrag.parentNode.removeChild(eleDrag);
    }
    this.style.color = "#000000";
    return false;
};
```

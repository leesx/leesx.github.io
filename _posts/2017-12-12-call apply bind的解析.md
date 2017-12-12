

call 和 apply 都是为了改变某个函数运行时的 context 即上下文而存在的，换句话说，就是为了改变函数体内部 this 的指向。
因为 JavaScript 的函数存在「定义时上下文」和「运行时上下文」以及「上下文是可以改变的」这样的概念。
二者的作用完全一样，只是接受参数的方式不太一样。
例如，有一个函数 func1 定义如下：
```
var func1 = function(arg1, arg2) {};
```
就可以通过 func1.call(this, arg1, arg2); 或者 func1.apply(this, [arg1, arg2]); 来调用。
其中 this 是你想指定的上下文，他可以任何一个 JavaScript 对象(JavaScript 中一切皆对象)，call 需要把参数按顺序传递进去，
而 apply 则是把参数放在数组里。
JavaScript 中，某个函数的参数数量是不固定的，因此要说适用条件的话，当你的参数是明确知道数量时，用 call，而不确定的时候，
用 apply，然后把参数 push 进数组传递进去。当参数数量不确定时，函数内部也可以通过 arguments 这个数组来便利所有的参数。

与call和apply不同的是，bind绑定后不会立即执行

```
add.bind(sub, 5, 3); //不再返回8
add.bind(sub, 5, 3)(); //8
```
如果bind的第一个参数是null或者undefined，等于将`this绑定到全局对象`。

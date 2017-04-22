---
layout:     post
title:      javascript 技术精要
subtitle:    "\"Hello World, Hello Blog\""
date:       2017-10-04
author:     leesx
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - javascript
---

> “🙉🙉🙉 ”



> 空数组与布尔值的比较

```
// 先执行[].toString() => "" ,!"" => true;  现在变为： [] == true => 最后结构就是true;

[] == ![]  


```

```
[] == false
/***
 引用数据类型与布尔类型比较，先将 引用数据类型转化为 基本数据类型
 [].toString() => "" => "" == false ==>比较结果就是true
 ***/

// js中的坑
1===1 ===1 // => false

true === (1===1) // => true
```

> 解释下 null 与 undefined 的区别

- null 是一个==空指针对象==，是一个可以被分配的值，设置为 null 的变量意味着其==无值==。所以 typeof null => "object"
- undefined 则代表着某个变量虽然声明了但是==尚未进行过任何赋值==。


> 浅拷贝

```
function copy(p){
  var c = {};
  for(var i in p){
   c[i]= p[i]
  }
  c.uber = p

  return c;
 }
```

> 深拷贝

```
function deepCopy(p,c){
 var c = c || {};
 for(var i in p){
   if(typeof p[i] === 'object'){
    c[i] = (p[i].constructor === Array) ? [] : {}
    deepCopy(p[i],c[i])
  }else{
	c[i] = p[i]
  }
 }
 return c
}
```
> 常用的继承方式

1. 仅从原型继承 prototype
```
function Animal(){
 this.home = '我属于动物园'
}

Animal.prototype.speak = function(){
 console.log(this.home)
}

function Dog(){

}

// 原型继承
//继承来自父级的prototype属性

Dog.prototype = Animal.prototype;
Dog.prototype.constructor = Dog;

var yellowDog = new Dog();

yellowDog.speak()

```

2. 原型链继承（仿传统）
```
function Animal(){
 this.home = '我属于动物园'
}

Animal.prototype.speak = function(){
 console.log(this.home)
}

function Dog(){
  //this.home = 'wawa'
}

// 原型继承
//继承来自父级的实例中，这种继承会继承父级的 <私有属性>

Dog.prototype = new Animal();
Dog.prototype.constructor = Dog;

var yellowDog = new Dog();

yellowDog.speak()

```
3. 借用构造函数法
```

function Animal(name,color){
    this.name = name;
    this.color = color;
}

Animal.prototype.speak = function(){
  console.log('这是'+this.name+',毛色是'+this.color)
}

function Dog(){
  Animal.apply(this,arguments) // 只能继承父级的私有属性。
}

var dog1 = new Dog('大黄',2)

```

4. 临时构造器法

```
 function extends(Child,Parent){
   // 1.创建一个临时的构造函数 F
   var F = function(){}
   // 2.临时构造函数F的原型 => 继承父级的原型prototype
   F.prototype = Parent.prototype;
   // 3. 子级的原型 => 继承 临时构造函数F 的实例
   Child.prototype = new F();
   // 4. 重置子级原型的构造函数
   Child.prototype.constructor = Child;

   // 在子级中新增一个属性 uber => 保存 一个父级原型的副本
   Child.uber = Parent.prototype;
 }

```

---

5. 使用Object.create()

> 简单来讲，new Object()是一种通过构造函数来创建object的方式，而Object.create(proto, [ propertiesObject ])
不需要通过构造函数就可以创建一个object，Object.create()的第一个参数是必须要的，第二个参数可选。其实Object.create()内部依然是通过new一个构造函数的方式来实现的，它有构造函数，不过这个构造函数是隐式存在的，看一下使老旧浏览器支持Object.create方法的“polyfill”就可以对它们之间的区别一目了然了：

```
if (!Object.create) {
    Object.create = function (o) {
    function F() {}  //定义了一个隐式的构造函数
    F.prototype = o;
    return new F();  //其实还是通过new来实现的
    };
  }
```

```
    function Car (desc) {
        this.desc = desc;
        this.color = "red";
    }

    Car.prototype = {
        getInfo: function() {
          return 'A ' + this.color + ' ' + this.desc + '.';
        }
    };
    //instantiate object using the constructor function
    var car =  Object.create(Car.prototype);
    car.color = "blue";
    alert(car.getInfo());
```

### XSS原理及防范

==Xss==(cross-site scripting)攻击指的是攻击者往Web页面里插入恶意html标签或者javascript代码。比如：攻击者在论坛中放一个 看似安全的链接，骗取用户点击后，窃取cookie中的用户私密信息；或者攻击者在论坛中加一个恶意表单， 当用户提交表单的时候，却把信息传送到攻击者的服务器中，而不是用户原本以为的信任站点。

#### XSS防范方法

1.代码里对==用户输入的地方和变量都需要仔细检查长度和对”<”,”>”,”;”,”’”等字符做过滤==；其次任何内容写到页面之前都必须加以encode，避免不小心把html tag 弄出来。这一个层面做好，至少可以堵住超过一半的XSS 攻击。

2.避免直接在cookie 中泄露用户隐私，例如email、密码等等。

3.通过==使cookie 和系统ip 绑定==来降低cookie 泄露后的危险。这样攻击者得到的cookie 没有实际价值，不可能拿来重放。

4.尽量==采用POST== 而非GET 提交表单

> new操作符具体干了什么呢

1、创建一个空对象，并且 this 变量引用该对象，同时还继承了该函数的原型。

2、属性和方法被加入到 this 引用的对象中。

3、新创建的对象由 this 所引用，并且最后隐式的返回 this 。
```
var obj  = {};
obj.__proto__ = Base.prototype;
Base.call(obj);
```
### 数组快速排序算法
[相关文章](http://www.ruanyifeng.com/blog/2011/04/quicksort_in_javascript.html)
> (1)在数据集之中，选择一个元素作为"基准"（pivot）。

> (2)所有小于"基准"的元素，都移到"基准"的左边；所有大于"基准"的元素，都移到"基准"的右边。

> (3)对"基准"左边和右边的两个子集，不断重复第一步和第二步，直到所有子集只剩下一个元素为止。

　　
==取数组中间的值当做参考值，并且把它从原数组中用splice方法截取==　　
```
function quickSort(arr){
  arr = arr || [];
  if(arr.length <= 1) return arr;
  var midIndex = Math.floor(arr.length/2)
  var midVal = arr.splice(midIndex,1)[0];

  var leftArr = [];
  var rightArr = [];

  for(var i=0;i<arr.length;i++){
	if(arr[i]<midVal){
    	leftArr.push(arr[i])
    }else{
		rightArr.push(arr[i])
    }
  }

  return quickSort(leftArr).concat(midVal,quickSort(rightArr))
}

```
==将数组的第一个元素当做参考值，for循环从1开始==
```
var a = [2,4,5,63,4,5,63,2,4,43];

function quicksort(arr)
{
    if (arr.length == 0)
        return [];

    var left = new Array();
    var right = new Array();
    var pivot = arr[0];

    for (var i = 1; i < arr.length; i++) {
        if (arr[i] < pivot) {
           left.push(arr[i]);
        } else {
           right.push(arr[i]);
        }
    }

    return quicksort(left).concat(pivot, quicksort(right));
}

console.log(quicksort(a));
```

#### 冒泡排序

```
function bubbleSort(array) {
    if (Object.prototype.toString.call(array).slice(8, -1) === 'Array') {
        var len = array.length,
            temp;
        for (var i = 0; i < len - 1; i++) {
            for (var j = len - 1; j >= i; j--) {
                if (array[j] < array[j - 1]) {
                    temp = array[j];
                    array[j] = array[j - 1];
                    array[j - 1] = temp;
                }
            }
        }
        return array;
    } else {
        return 'array is not an Array!';
    }
}
```

```
function bubbleSort(arr){
  for(var i=0;i<arr.length-1;i++){
    for(var j=i+1;j<arr.length;j++){
      if(arr[i]>arr[j]){
         var temp
         temp = arr[i];
         arr[i]=arr[j];
         arr[j] = temp
       }
    }
  }
  return arr;
}

```

####  一张图理解prototype、proto和constructor的三角关系

参考 [prototype、proto和constructor的三角关系](http://www.cnblogs.com/xiaohuochai/p/5721552.html)

####  闭包

```
function f1(){
　　　　n=999;
　　　　function f2(){
　　　　　　alert(n);
　　　　}
　　　　return f2;
　　}
　　var result=f1();
　　result(); // 999
　　
```

> 各种专业文献上的“闭包”（closure）定义非常抽象，很难看懂。我的理解是，闭包就是能够读取其他函数内部变量的函数。
由于在Javascript语言中，只有函数内部的子函数才能读取局部变量，因此可以把闭包简单理解成“定义在一个函数内部的函数”。
所以，在本质上，闭包就是将函数内部和函数外部连接起来的一座桥梁。

- 它的最大用处有两个，一个是前面提到的可以读取函数内部的变量，另一个就是让这些变量的值始终保持在内存中。

1. 由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。
2. 闭包会在父函数外部，改变父函数内部变量的值。所以，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便
改变父函数内部变量的值。

### 关于原型的几点

1.所有的对象都有prototype 和 __proto__属性 。
2.所有的构造函数（原生的如Object Function Date 等以及自己声明的构造函数）都是对象，因此，他们都包含prototype和__proto__属性

```
Function.__proto__ === Function.prototype === Object.__proto__
```
从上面代码可以看出，Object是通过Function 构造出的。

1.所有的普通函数都是通过new Function生成的，所以，每一个函数都是Function的实例。
```
function fn(){}

fn.__proto__ === Function.prototype  

```

```
function fn1(){
    console.log(1)
}

Function.prototype.call.call(fn1)  //相当于fn1执行。
Function.prototype.call.call.call.call(fn1)  //相当于fn1执行。
```

### 柯理化函数

> 可以简单的理解为函数中返回函数的形式。

```
function fnc(){
    return fn(){

    }
}
```
```
//实例 bind this

function bindThis(bindObj,fn){

    return function(){
        //将类数组转化为数组
        // var arg = [].slice(arguments,0)
        var arg = Array.prototype.slice(arguments,0)

        fn.apply(bindObj,arg)
    }
}

//在原型上添加bind

if(!Function.prototype.bind){

    Function.prototype.bind = function(obj){
        var args = [].slice.call(arguments,1)
        return function(){
            var innerArgs = [].slice.call(arguments,0)
            var finalArgs = args.concat(innerArgs)
            this.apply(obj,finalArgs)
        }
    }
}


```

### 常用方法

- 数组去重
```
//es6 先转化为Set数据结构再，通过扩展运算符(...)，转化为数组
//扩展运算符（...）内部使用for...of循环
[...new Set(arr)]

```
- 类数组转化为数组
```
//es6 通过扩展运算符(...)转化
[...document.querySeletorAll('div')]

// es6  Array.from()
Array.from(new Set(document.querySeletorAll('div')))

//es5 借用数组的slice方法 拷贝一份

// [].slice.call(likeArray)
Array.prototype.slice.call(document.querySeletorAll('div'),0)
Array.prototype.slice.call(arguments,0)
```

### 正则提取url中的参数

- 获取指定URL参数
```
function getUrlParams(name) {
    var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)", "i"); //定义正则表达式
    var r = window.location.search.substr(1).match(reg);  
    if (r != null) return unescape(r[2]);
  return null;
}

window.location = "http://www.baidu.com?name=elephant&age=25&sex=male";
var name = getUrlParams("name"); //elephant
var age = getUrlParams("age");   //25
var sex = getUrlParams("sex");　//male
```

> 首先理解定义的正则表达式，（^|&）意思是从头开始匹配字符&, =([^&]*)意思是匹配=后面零个或多个不是&的字符，直至碰到第一个&为止，(&|$)意思就是匹配最后一个&，在正则表达式中，增加一个()代表着匹配数组中增加一个值, 因此代码中的正则匹配后数组中应包含4个值, 在getUrlParams("name")函数中，此时 r 获取到的数组应该是 ["name=elephant&", "", "elephant", "&"]代码中 window.location.search 意思是取window.location中 '?'后面的值包括'?',因此所得到的值是"?name=elephant&age=25&sex=male"
substr()这个函数的作用是截取字符串，在代码中window.location.search.surstr(1) 意思就是截取第一个字符后面的字符串，所得到的值即是"name=elephant&age=25&sex=male" unescape这个函数的作用是解码escape编码后的字符串

- 获取所有的URL参数
```
function parse_url(_url){ //定义函数
　　var pattern = /(\w+)=(\w+)/ig;//定义正则表达式
　　var parames = {};//定义数组
　　url.replace(pattern, function(a, b, c){
　　　　parames[b] = c;
　　});
　　return parames;//返回这个数组.
}

var  url = "http://www.baidu.com?name=elephant&age=25&sex=male"
var params = parse_url(url);// ["name=elephant", "age=25", "sex=male"]
```
> 当replace匹配到name=elephant时.那么就用执行function(a,b,c);其中a的值为:name=elephant,b的值为name,c的值为elephant;(这是反向引用.因为在定义正则表达式的时候有两个子匹配.)，然后将数组的key为name的值赋为elephant;然后完成.
再继续匹配到age=25;此时执行function(a,b,c);其中a的值为:age=25,b的值为age,c的值为25;然后将数组的key为id的值赋为25.



—— leesx 后记于 2017.2

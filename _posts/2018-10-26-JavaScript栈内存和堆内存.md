栈内存和堆内存
-------

> JavaScript中的变量分为基本类型和引用类型
> 
> 基本类型是保存在栈内存中的简单数据段，它们的值都有固定的大小，保存在栈空间，通过按值访问
> 
> 引用类型是保存在堆内存中的对象，值大小不固定，栈内存中存放的该对象的访问地址指向堆内存中的对象，JavaScript不允许直接访问堆内存中的位置，因此操作对象时，实际操作对象的引用

### 结合代码与图来理解

    let a1 = 0; // 栈内存
    let a2 = "this is string" // 栈内存
    let a3 = null; // 栈内存
    let b = { x: 10 }; // 变量b存在于栈中，{ x: 10 }作为对象存在于堆中
    let c = [1, 2, 3]; // 变量c存在于栈中，[1, 2, 3]作为对象存在于堆中

![](./../img/20181026_1.png)

当我们要访问堆内存中的引用数据类型时

1.  从栈中获取该对象的地址引用
2.  再从堆内存中取得我们需要的数据

### 基本类型发生复制行为

    let a = 20;
    let b = a;
    b = 30;
    console.log(a); // 20

结合下面图进行理解：

![](./../img/20181026_2.png)

**在栈内存中的数据发生复制行为时，系统会自动为新的变量分配一个新值，最后这些变量都是相互独立互不影响的**

### 引用类型发生复制行为

    let a = { x: 10, y: 20 }
    let b = a;
    b.x = 5;
    console.log(a.x); // 5

1.  引用类型的复制，同样为新的变量b分配一个新的值，保存在栈内存中，不同的是，这个值仅仅是引用类型的一个地址指针
2.  他们两个指向同一个值，也就是地址指针相同，在堆内存中访问到的具体对象实际上是同一个
3.  因此改变b.x时，a.x也发生了变化，这就是引用类型的特性
4.  结合下图理解

![](./../img20181026_3.png)

### 总结

![](./../img/20181026_4.png)

文章来源——[探索Virtual DOM的前世今生](https://zhuanlan.zhihu.com/p/35876032)

## 缘起

在前端开发过程中，对性能产生最大影响的因素莫过于DOM的重排重绘了，React作为前端框架领跑者，为了有效解决DOM更新开销的问题，采用了Virtual DOM的思路，不仅提升了DOM操作的效率，更推动了数据驱动式组件开发的形成与完善。一旦习惯了数据驱动式开发，再要求我们使用显式DOM操作开发的话，虐心程度无异于春节返乡的车票卖完了，只能坐长途辗转煎熬了。



![](https://pic1.zhimg.com/v2-521696e3d813bab387c939ed7d5af064_b.jpg)

![](https://pic1.zhimg.com/80/v2-521696e3d813bab387c939ed7d5af064_hd.jpg)

而VirtualDOM的主要思想就是模拟DOM的树状结构，在内存中创建保存映射DOM信息的节点数据，在由于交互等因素需要视图更新时，先通过对节点数据进行diff后得到差异结果后，再一次性对DOM进行批量更新操作，这就好比在内存中创建了一个平行世界，浏览器中DOM树的每一个节点与属性数据都在这个平行世界中存在着另一个版本的虚拟DOM树，所有复杂曲折的更新逻辑都在平行世界中的VirtualDOM处理完成，只将最终的更新结果发送给浏览器中的DOM树执行，这样就避免了冗余琐碎的DOM树操作负担，进而有效提高了性能。


![](https://pic4.zhimg.com/v2-5989c613dd021fa8a3c4d8a3dde42cff_b.jpg)

![](https://pic4.zhimg.com/80/v2-5989c613dd021fa8a3c4d8a3dde42cff_hd.jpg)</figure>

如果你已经是熟练使用vue或者react的项目老手，本文将助你一探这些前端框架进行视图更新背后的工作原理，并且可以一定程度掌握VirtualDOM的核心算法，即便你还未享受过这些数据驱动的工具带来的便利，通过阅读本文，你也将了解到一些当下的前端框架是如何对开发模式产生巨变影响的。同时本文也是我们对相关知识学习的一个总结，难免有误，欢迎多多指正，并期待大大们的指点。

## Diff效率之争

VirtualDOM是react在组件化开发场景下，针对DOM重排重绘性能瓶颈作出的重要优化方案，而他最具价值的核心功能是如何识别并保存新旧节点数据结构之间差异的方法，也即是**diff算法**。毫无疑问的是diff算法的复杂度与效率是决定VirtualDOM能够带来性能提升效果的关键因素。因此，在VirtualDOM方案被提出之后，社区中不断涌现出对diff的改进算法，引用司徒正美的经典介绍：

> 最开始经典的深度优先遍历DFS算法，其复杂度为O(n^3)，存在高昂的diff成本，然后是cito.js的横空出世，它对今后所有虚拟DOM的算法都有重大影响。它采用两端同时进行比较的算法，将diff速度拉高到几个层次。紧随其后的是kivi.js，在cito.js的基出提出两项优化方案，使用key实现移动追踪及基于key的编辑长度距离算法应用（算法复杂度 为O(n^2)）。但这样的diff算法太过复杂了，于是后来者snabbdom将kivi.js进行简化，去掉编辑长度距离算法，调整两端比较算法。速度略有损失，但可读性大大提高。再之后，就是著名的vue2.0 把snabbdom整个库整合掉了。

因此目前VirtualDOM的主流diff算法趋向一致，在主要diff思路上，snabbdom与react的reconilation方式基本相同。

## Diff主要策略

*   **按tree层级diff(level by level)**

由于diff的数据结构是以DOM渲染为目标的模拟树状层级结构的节点数据，而在WebUI中很少出现DOM的层级结构因为交互而产生更新，因此VirtualDOM的diff策略是在新旧节点树之间按层级进行diff得到差异，而非传统的按深度遍历搜索，这种通过大胆假设得到的改进方案，不仅符合实际场景的需要，而且大幅降低了算法实现复杂度，从O(n^3)提升至O(n)。

<figure>

<noscript>![](https://pic2.zhimg.com/v2-66d26903749c0ac081f8caa9ef649375_b.jpg)</noscript>

![](https://pic2.zhimg.com/80/v2-66d26903749c0ac081f8caa9ef649375_hd.jpg)</figure>

*   **按类型进行diff**

无论VirtualDOM中的节点数据对应的是一个原生的DOM节点还是vue或者react中的一个组件，不同类型的节点所具有的子树节点之间结构往往差异明显，因此对不同类型的节点的子树进行diff的投入成本与产出比将会很高昂，为了提升diff效率，VirtualDOM只对相同类型的同一个节点进行diff，当新旧节点发生了类型的改变时，则并不进行子树的比较，直接创建新类型的VirtualDOM，替换旧节点。

<figure>

<noscript>![](https://pic3.zhimg.com/v2-7a8223157d138dde39c32e6edd42b0b2_b.jpg)</noscript>

![](https://pic3.zhimg.com/80/v2-7a8223157d138dde39c32e6edd42b0b2_hd.jpg)</figure>

*   **列表diff**

当被diff节点处于同一层级时，通过三种节点操作新旧节点进行更新：插入，移动和删除，同时提供给用户设置key属性的方式调整diff更新中默认的排序方式，在没有key值的列表diff中，只能通过按顺序进行每个元素的对比，更新，插入与删除，在数据量较大的情况下，diff效率低下，如果能够基于设置key标识尽心diff，就能够快速识别新旧列表之间的变化内容，提升diff效率。

<figure>

<noscript>![](https://pic2.zhimg.com/v2-54c7c636c5586881665b4edeef221ad1_b.jpg)</noscript>

![](https://pic2.zhimg.com/80/v2-54c7c636c5586881665b4edeef221ad1_hd.jpg)</figure>

## Virtual DOM不同的实现方式

基于以上的三条diff原则，我们就可以自由选择Virtual DOM的具体方案，甚至自己动手进行diff实践，在那之前，让我们先以Vue中的snabbdom与React中的Reconcile这两个Virtual DOM的实现方案为对象进行学习。

## snabbdom的vnode

在众多VirtuaDOM实现方案中，snabbdom以其高效的实现，小巧的体积与灵活的可扩展性脱颖而出，它的核心代码只有300行+，却已被适用于vue等轻量级前端框架中作为VirtualDOM的主要功能实现。

一个使用snabbdom创建的demo是这样的：

<div class="highlight">

    import snabbdom from 'snabbdom';
    import h from 'snabbdom/h';
    const patch = snabbdom.init([
      require('snabbdom/modules/class'),          // makes it easy to toggle classes
      require('snabbdom/modules/props'),          // for setting properties on DOM elements
      require('snabbdom/modules/style'),          // handles styling on elements with support for animations
      require('snabbdom/modules/eventlisteners'), // attaches event listeners
    ]);

    var vnode = h('div', {style: {fontWeight: 'bold'}}, 'Hello world');
    patch(document.getElementById('placeholder'), vnode)

</div>

在snabbdom中提供了h函数做为创建VirtualDOM的主要函数，h函数接受的三个参数同时揭示了diff算法中关注的三个核心：节点类型，属性数据，子节点对象。而patch方法即是用来创建初始DOM节点与更新VirtualDOM的diff核心函数。

<div class="highlight">

    function view(name) { 
      return h('div', [
        h('input', {
          props: { type: 'text', placeholder: 'Type a your name' },
          on   : { input: update }
        }),
        h('hr'),
        h('div', 'Hello ' + name)
      ]); 
    }

    var oldVnode = document.getElementById('placeholder');

    function update(event) {
      const newVnode = view(event.target.value);
      oldVnode = patch(oldVnode, newVnode);
    }

    oldVnode = patch(oldVnode, view(''));

</div>

以上是一个通过input事件触发VirtualDOM更新的典型app。在h函数中，不光可以为VirtualDOM保存数据属性，还可以设置事件回调函数，并在其中获取并处理相关的事件属性，如update回调中的event对象。通过捕获事件中创建新的vnode，与旧的vnode进行diff，最终对当前的oldVnode进行更新，并向用户展示更新结果，他的工作流程如下：

<figure>

<noscript>![](https://pic1.zhimg.com/v2-490c8418f0e4b7905074e1429b71490c_b.jpg)</noscript>

![](https://pic1.zhimg.com/80/v2-490c8418f0e4b7905074e1429b71490c_hd.jpg)</figure>

在snabbdom源码中的核心patch函数中很明显的体现了VirtualDOM的按类型diff与列表diff的策略：如果patch的新旧节点经过sameVnode判断不是同一个节点，则进行新节点的创建插入与旧节点的删除，而sameVnode也即是判断两个节点是否有相同的key标识与传入的带有节点类型等信息的selector字符串是否相同为依据的：

<div class="highlight">

    function sameVnode(vnode1, vnode2) {
        return vnode1.key === vnode2.key && vnode1.sel === vnode2.sel;
    }

</div>

而对相同节点进行新旧diff的主函数patchVnode的实现流程如下，其中oldCh与ch为保存旧的当前节点与将要更新的新节点：

<div class="highlight">

    //新的text不存在
            if (isUndef(vnode.text)) {
                if (isDef(oldCh) && isDef(ch)) {
                    if (oldCh !== ch)
                        updateChildren(elm, oldCh, ch, insertedVnodeQueue);
                }
                //旧的子节点不存在，新的存在
                else if (isDef(ch)) {
                    //旧的text存在
                    if (isDef(oldVnode.text))
                        api.setTextContent(elm, '');
                    //把新的插入到elm底下
                    addVnodes(elm, null, ch, 0, ch.length - 1, insertedVnodeQueue);
                }
                //新的子节点不存在,旧的存在
                else if (isDef(oldCh)) {
                    removeVnodes(elm, oldCh, 0, oldCh.length - 1);
                }
                //新的子节点不存在,旧的text存在
                else if (isDef(oldVnode.text)) {
                    api.setTextContent(elm, '');
                }

</div>

1.  如果新节点可能是复杂节点而非text节点，则对节点的children进一步diff：先判断是否存在新节点的children整体新增或删除的情况，若是则进行批量更新， 而新旧节点都包含children列表的情况进行updateChildren处理
2.  如果新旧节点都是text节点，且两者不同则只进行text更新即可

以下介绍updateChildren的核心diff方式，以旧节点oldCh为当前VirtualDOM状态，将新节点newCh的变化对oldCh进行更新得到新的VirtualDOM状态，并记录新旧节点的startIndex与endIndex两端同时比较，这样会比从单向按顺序对比的方式更快得到diff结果：

*   当新旧节点的startVnode与endVnode 各自对应相同时，继续对比，startVnode与endVnode位置各自向中间移动一位。
*   发现oldStartVnode，newEndVnode相同时，也就是oldStartVnode成为了新的末端节点，就将oldStartVnode插到oldEndVnode的后一个位置

<figure>

<noscript>![](https://pic1.zhimg.com/v2-b25d24924cdfac233afbbed716c185bc_b.jpg)</noscript>

![](https://pic1.zhimg.com/80/v2-b25d24924cdfac233afbbed716c185bc_hd.jpg)</figure>

*   当oldEndVnode，newStartVnode相同时，也就是oldEndVnode成为了新的头部节点，就将oldEndVnode插入到oldStartVnode前一个位置

<figure>

<noscript>![](https://pic1.zhimg.com/v2-6c5dd64bb248c3621d7f1ce0c82b5dd4_b.jpg)</noscript>

![](https://pic1.zhimg.com/80/v2-6c5dd64bb248c3621d7f1ce0c82b5dd4_hd.jpg)</figure>

*   当发现oldCh里没有当前newCh中的节点，将新节点插入到oldStartVnode的前边，同时这里会借助节点中的key值进行map查找是否在其他位置中有匹配的旧节点，如果有匹配，就对旧节点进行更新，再将其插入到当前的oldStartVnode的前面。

<figure>

<noscript>![](https://pic4.zhimg.com/v2-4947f6db26ad8bfaaf2379d3b8be4e5f_b.jpg)</noscript>

![](https://pic4.zhimg.com/80/v2-4947f6db26ad8bfaaf2379d3b8be4e5f_hd.jpg)</figure>

*   在这一轮对比结束时后，有两种情况，当oldStartIdx > oldEndIdx，说明旧节点oldCh已经遍历完。那么剩下newStartIdx和newEndIdx之间的vnode的新节点就调用addVnodes，批量插入父节点的before节点位置，before很多时候是为null的。addVnodes调用的是insertBefore操作dom节点，我们看看insertBefore的文档：parentElement.insertBefore(newElement, referenceElement)如果referenceElement为null则newElement将被插入到子节点的末尾。如果newElement已经在DOM树中，newElement首先会从DOM树中移除。所以before为null，newElement将被插入到子节点的末尾。
*   如果newStartIdx > newEndIdx，就是newCh先在第一轮对比中遍历完。此时oldCh中的oldStartIdx和oldEndIdx之间的vnode是需要被删除的，调用removeVnodes将它们从dom里删除。

## React的reconcilation

在react的历史版本中，完成数据节点diff的过程是reconcilation,，当你在一个组件中调用setState时，react会将该组件节点标记为dirty，进行reconcile并得到重新构建的子树virtual-dom，在工作流结束时重新render带有dirty标记的节点， 如果你是在组件的根节点上进行setState，那么整个组件树Virtual DOM都会重新创建，但由于这并不是直接操作真实的DOM，所以实际上产生的影响仍然有限。

在React16的重写中，最重要的改变时将核心架构改为了代号为Fiber的异步渲染架构。从本质上看，一个Fiber就是一个POJO对象，一个React Element可以对应一个或多个Fiber节点，Fiber包含着DOM节点与React组件中的所有工作需要的属性数据。因此虽然React的代码中其实没有明确的Virtual DOM概念，但通过对Fiber的设计充分完成了Virtual DOM的功能与机制。

Fiber除了承担Virtual DOM的工作之外，它真正设计目的是实现一种在前端执行的轻量执行线程，同普通线程一样共享定址空间，但却能够受React自身的Fiber系统调度，实现渲染任务细分，可计时，可打断，可重启，可调度的协作式多任务处理的强大渲染任务控制机制。

<figure>

<noscript>![](https://pic4.zhimg.com/v2-c47085867c8bab9ea23f9325a71fed47_b.jpg)</noscript>

![](https://pic4.zhimg.com/80/v2-c47085867c8bab9ea23f9325a71fed47_hd.jpg)</figure>

言归正传，尽管Fiber异步渲染的机制几乎重写了整个reconcile的过程，但通过源码分析可以看到对节点reconcile的思路与16之前版本基本一致：

在react的16.3.1版本中，会在页面初始化render运行过程中，对应页面结构创建FiberNode，通过child属性与siblings属性分别存放子节点与兄弟节点，同时使用return属性标记父节点，便于遍历与修改。Fiber在update的时候，会从原来的Fiber（我们称为current）clone出一个新的Fiber（称为alternate）。两个Fiber diff出的变化（side effect）记录在alternate上。所以一个组件在更新时最多会有两个Fiber与其对应，在更新结束后alternate会取代之前的current的成为新的current节点。

<figure>

<noscript>![](https://pic4.zhimg.com/v2-2162565351cdc2b52a3340fbf5befbf3_b.jpg)</noscript>

![](https://pic4.zhimg.com/80/v2-2162565351cdc2b52a3340fbf5befbf3_hd.jpg)</figure>

这里略过Fiber复杂的构建过程，我们直接来看在某个组件需要更新时的内部机制，也就是组件中setState方法被调用后，首先会在该组件对应的Fiber节点中设置updateQueue属性以队列的形式存储更新内容，然后从顶端开始对整个Fiber树开始进行深度遍历，查找到需要进行更新的Fiber节点，判断的依据就是该节点是否有updateQueue中的更新内容，如果存在更新，就运行我们熟知的shouldUpdateComponent函数来判断，shouldUpdateComponent返回为真，就执行componentWillUpdate函数，并根据其节点类型决定按哪种方式进行更新，也就是运行reconcile机制进行diff，如果diff的是component节点，待diff完成之后再运行lifeCycle中的componentDidUpdate函数。

<div class="highlight">

    const shouldUpdate = checkShouldComponentUpdate(
          workInProgress,
          oldProps,
          newProps,
          oldState,
          newState,
          newContext,
        );

        if (shouldUpdate) {
          // 【译】这是为了支持react-lifecycles-compat的兼容组件
          // 使用新的API的时候不能调用非安全的生命周期钩子
          if (
            !hasNewLifecycles &&
            (typeof instance.UNSAFE_componentWillUpdate === 'function' ||
              typeof instance.componentWillUpdate === 'function')
          ) {
            //开始计时componentWillUpdate阶段
            startPhaseTimer(workInProgress, 'componentWillUpdate');
            //执行组件实例上的componentWillUpdate钩子
            if (typeof instance.componentWillUpdate === 'function') {
              instance.componentWillUpdate(newProps, newState, newContext);
            }
            if (typeof instance.UNSAFE_componentWillUpdate === 'function') {
              instance.UNSAFE_componentWillUpdate(newProps, newState, newContext);
            }
            //结束计时componentWillUpdate阶段
            stopPhaseTimer();
          }
          // 在当前工作中的Fiber打上标签，后续执行componentDidUpdate钩子
          if (typeof instance.componentDidUpdate === 'function') {
            workInProgress.effectTag |= Update;
          }
          if (typeof instance.getSnapshotBeforeUpdate === 'function') {
            workInProgress.effectTag |= Snapshot;
          }
        } else {
          // 【译】如果当前节点已经在更新中，即使我们终止了更新，仍然应该执行componentDidUpdate钩子
          if (typeof instance.componentDidUpdate === 'function') {
            if (
              oldProps !== current.memoizedProps ||
              oldState !== current.memoizedState
            ) {
              workInProgress.effectTag |= Update;
            }
          }
          if (typeof instance.getSnapshotBeforeUpdate === 'function') {
            if (
              oldProps !== current.memoizedProps ||
              oldState !== current.memoizedState
            ) {
              workInProgress.effectTag |= Snapshot;
            }
          }

</div>

这里提到，我们在组件中setState之后，React会将其视为dirty节点，在事件流结束后，找出dirty的组件节点并进行diff，值得注意的是，虽然重新render构建一颗新的Virtual DOM树不会触碰真正的DOM，这里也并没有重新创建新的Fiber树，取而代之的是在每个Fiber节点中都设置了alternate属性与current属性来分别存放用于更新替代与当前的节点版本，只是在重新遍历整颗树后找到dirty的节点生成新的Fiber节点用于更新：

<figure>

<noscript>![](https://pic4.zhimg.com/v2-fc2c10b3b653e969278efd0eff391907_b.jpg)</noscript>

![](https://pic4.zhimg.com/80/v2-fc2c10b3b653e969278efd0eff391907_hd.jpg)</figure>

正如react官方文档中描述的一样，当一个节点需要被更新时（shouldComponentUpdate），下一步则需要对它及其子节点进行shouldComponentUpdate判断与Reconcile的过程来对节点进行更新，这里我们可以通过在组件中写入覆盖的shouldComponentUpdate函数来决定是否进行更新的逻辑：

<figure>

<noscript>![](https://pic1.zhimg.com/v2-f43f9ff591dbf8438a732023220fea8c_b.jpg)</noscript>

![](https://pic1.zhimg.com/80/v2-f43f9ff591dbf8438a732023220fea8c_hd.jpg)</figure>

Reconcile过程的核心源代码起始于reconcileChildFiber函数，主要实现方式是：根据传入组件的类型进行不同的reconcile过程，其中最为复杂的是传入子组件数组调用reconcileChildrenArray处理的情况。reconcileChildrenArray函数在开始进行新旧子节点数组reconcile时，默认先按index顺序进行对比，由于Fiber节点本身没有设置向后指针，因此React目前没有采取两端同时对比的算法，也就是说每一个同层级别的兄弟Fiber节点只能指向下一个节点。因此在通常情况下，对比过程中react只会调用updateSlot将得到的新Fiber数据按其不同类型直接更新到旧Fiber的位置中。

在按顺序对比中，如果使用updateSlot未发现key值不相等的情况，则进行将老节点替换成为新节点，第一轮遍历完成后，则判断如果是新节点已遍历完成，就将剩余的老节点批量删除，如果是老节点遍历完成仍有新节点剩余，则将新节点批量插入老节点末端，如果在第一轮遍历中发现key值不相等的情况，则直接跳出以上步骤，按照key值进行遍历更新，最后再删除没有被上述情况涉及的元素，由此可见在列表结构的组件中，添加key值是有助于提升diff算法效率的。

以下是reconcileChildrenArray函数源代码：

<div class="highlight">

    // react使用flow进行类型检查
    function reconcileChildrenArray(
        returnFiber: Fiber,
        currentFirstChild: Fiber | null,
        newChildren: Array<*>,
        expirationTime: ExpirationTime,
      ): Fiber | null {
        let resultingFirstChild: Fiber | null = null;
        let previousNewFiber: Fiber | null = null;

        let oldFiber = currentFirstChild;
        let lastPlacedIndex = 0;
        let newIdx = 0;
        let nextOldFiber = null;

        for (; oldFiber !== null && newIdx < newChildren.length; newIdx++) {
          // 没有采用两端同时对比，受限于Fiber列表的单向结构
          if (oldFiber.index > newIdx) {
            nextOldFiber = oldFiber;
            oldFiber = null;
          } else {
          // 指向下一个旧的兄弟节点
            nextOldFiber = oldFiber.sibling;
          }
          // 尝试使用新的Fiber更新旧节点
          const newFiber = updateSlot(
            returnFiber,
            oldFiber,
            newChildren[newIdx],
            expirationTime,
          );
    、    //如果在遍历中发现key值不相等的情况，则直接跳出第一轮遍历
          if (newFiber === null) {
            if (oldFiber === null) {
              oldFiber = nextOldFiber;
            }
            break;
          }
          if (shouldTrackSideEffects) {
            if (oldFiber && newFiber.alternate === null) {
             // 【译】我们找到了匹配的节点，但我们并不保留当前的Fiber,所以我们需要删除当前的子节点
              deleteChild(returnFiber, oldFiber);
            }
          }
          lastPlacedIndex = placeChild(newFiber, lastPlacedIndex, newIdx);
          // 记录上一个更新的子节点
          if (previousNewFiber === null) {  
            resultingFirstChild = newFiber;
          } else { 
            previousNewFiber.sibling = newFiber;
          }
          previousNewFiber = newFiber;
          oldFiber = nextOldFiber;
        }

        if (newIdx === newChildren.length) {
          // 【译】我们已经遍历完了所有的新节点，直接删除剩余旧节点
          deleteRemainingChildren(returnFiber, oldFiber);
          return resultingFirstChild;
        }

        if (oldFiber === null) {
          // 如果旧节点先遍历完，则按顺序插入剩余的新节点，这里受限于Fiber的结构比较繁琐
          for (; newIdx < newChildren.length; newIdx++) {
            const newFiber = createChild(
              returnFiber,
              newChildren[newIdx],
              expirationTime,
            );
            if (!newFiber) {
              continue;
            }
            lastPlacedIndex = placeChild(newFiber, lastPlacedIndex, newIdx);
            if (previousNewFiber === null) {
              // TODO: Move out of the loop. This only happens for the first run.
              resultingFirstChild = newFiber;
            } else {
              previousNewFiber.sibling = newFiber;
            }
            previousNewFiber = newFiber;
          }
          return resultingFirstChild;
        }

        // 【译】把子节点都设置快速查找的map映射集
        const existingChildren = mapRemainingChildren(returnFiber, oldFiber);

        // 【译】使用map查找需要保存或删除的节点
        for (; newIdx < newChildren.length; newIdx++) {
          // 按map查找并创建新的Fiber
          const newFiber = updateFromMap(
            existingChildren,
            returnFiber,
            newIdx,
            newChildren[newIdx],
            expirationTime,
          );
          if (newFiber) {
            if (shouldTrackSideEffects) {
              if (newFiber.alternate !== null) {
                // 【译】新的Fiber也是一个工作线程，但是如果已有当前的实例，那我们就可以复用这个Fiber,
                // 我们要从列表中删除这个新的，避免准备复用的Fiber被删除
                existingChildren.delete(
                  newFiber.key === null ? newIdx : newFiber.key,
                );
              }
            }
            // 插入当前更新位置
            lastPlacedIndex = placeChild(newFiber, lastPlacedIndex, newIdx);
            if (previousNewFiber === null) {
              resultingFirstChild = newFiber;
            } else {
              previousNewFiber.sibling = newFiber;
            }
            previousNewFiber = newFiber;
          }
        }

        if (shouldTrackSideEffects) 
          // 【译】到此所有剩余的子节点都将被删除，加入删除队列
          existingChildren.forEach(child => deleteChild(returnFiber, child));
        }
        //最终返回Fiber子节点列表的第一个节点
        return resultingFirstChild;
      }

</div>

## 结束语：

VirtualDOM的设计是提升前端渲染性能的有效方案，也因此提供了以数据为驱动的前端框架工具的基础，将我们从DOM的繁琐操作中解放出来，不同的VirtualDOM方案在diff方面基本基于三条diff原则，具体diff过程则考虑自身运行上下文中的数据结构，算法效率，组件生命周期与设计来选择diff实现。例如上文snabbdom的updateChildren执行中使用了两端同时对比以及根据位置顺序进行移动的更新策略，而React则受限于Fiber的单向结构采用按顺序直接替换的方式更新，但React优化的组件设计与Fiber的工作线程机制在整体渲染性能方面带来了效率提升，同时两者都提供了基于key值进行diff的策略改善方式。

VirtualDOM的设计影响深远，本文仅对VirtualDOM中的思想与diff实现进行了详细介绍，此外，如何创建一个VirtualDOM树，如何将diff结果进行patch更新等内容仍有许多不同的具体实现方式可以进行探索，以及React16的Fiber机制更是在异步渲染方面上又进了一步，值得我们持续关注与学习。

## 参考阅读

diff算法类：

[snabbdom源码](https://github.com/snabbdom/snabbdom)

[React-less Virtual DOM with Snabbdom :functions everywhere!](https://medium.com/@yelouafi/react-less-virtual-dom-with-snabbdom-functions-everywhere-53b672cb2fe3)

[解析 snabbdom 源码，教你实现精简的 Virtual DOM 库](https://github.com/creeperyang/blog/issues/33)

**[React’s diff algorithm](https://calendar.perfplanet.com/2013/diff/)**

[Snabbdom - a Virtual DOM Focusing on Simplicity - Interview with Simon Friis Vindum](https://survivejs.com/blog/snabbdom-interview/)

[去哪儿网迷你React的研发心得](https://segmentfault.com/a/1190000011235844)

Fiber介绍类

[React Fiber Architecture](https://github.com/acdlite/react-fiber-architecture)

[如何理解 React Fiber 架构？](https://www.zhihu.com/question/49496872)

[React 16 Fiber源码速览](https://zxc0328.github.io/2017/09/28/react-16-source/)

[How React Fiber can make your web and mobile apps smoother and more responsive](https://www.monterail.com/blog/2017/react-fiber-release)

[React的新引擎—React Fiber是什么？](https://code.facebook.com/posts/1716776591680069/react-16-a-look-inside-an-api-compatible-rewrite-of-our-frontend-ui-library/)

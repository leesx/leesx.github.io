![](https://upload-images.jianshu.io/upload_images/1181204-9839ed98980d16e3.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

### useState

- 使用状态
```
const [n, setN] = React.useState(0)
const [user, setUser] = React.useState({name: 'Jack', age: 18})
```
- 注意事项

1. 不可局部更新
> 如果state是一个对象，能否部分setState？

答案是不行，因为setState不会帮我们合并属性,setState每次更新都会覆盖原对象
那么useReducer会合并属性吗？也不会！
因为React认为这应该是你自己要做的事情
2. setState(obj) 如果obj地址不变，那么React就认为数据没有变化，不会更新视图

- useState接受函数 可以用来初始化state
```
const [state, setState] = useState(() => {return initialState}) // 该函数返回初始state，且只执行一次
```
- setState接受函数 可以在回调中获取上一次的state，也建议优先考虑使用这种方式
![](https://upload-images.jianshu.io/upload_images/1181204-84c1120b032902ff.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

### useReducer

1. 创建initalState
2. 创建reducer(state, action)
3. 使用 [state, dispatch] = useReducer(reducer, initalState)
4. dispatch({})

![](https://upload-images.jianshu.io/upload_images/1181204-04fcd86511ef9b18.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

### useEffect

- 副作用 (API 名字叫得不好)
> 对环境的改变即为副作用，如修改 document.title

但我们不一定非要把副作用放在 useEffect 里面

实际上叫做 afterRender 更好，每次render后执行
如果同时存在多个 useEffect， 会按照出现次序执行

- 用途
> 一、作为 componentDidMount 使用，[ ] 作第二个参数

二、作为 componentDidUpdate 使用，可指定依赖

三、作为 componentWillUnmount 使用，通过 return

四、以上三种用途可同时存在

### useLayoutEffect 布局副作用
- useLayoutEffect 在浏览器渲染之前执行
- useEffect 在浏览器渲染之后执行

> useLayoutEffect 总比 useEffect 先执行
> useLayoutEffect 里的任务最好影响了 Layout
> 为了用户体验，优先使用 useEffect (优先渲染)

- useMemo 与 useCallback
`useCallback(x => console.log(x), [m])` 等价于 `useMemo( () => x => console.log(x), [m])`

React.memo 其实是对props进行了浅比较，如果props的key是引用类型或者事件绑定，都会导致比较失败，重新渲染
useMemo和useCallback都会在组件第一次渲染的时候执行，之后会在其依赖的变量发生改变时再次执行；并且这两个hooks都返回缓存的值，useMemo返回 缓存的变量，useCallback返回 缓存的函数

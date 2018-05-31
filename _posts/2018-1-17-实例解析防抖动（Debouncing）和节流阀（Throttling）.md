防抖（Debounce）和节流（throttle）都是用来控制某个函数在一定时间内执行多少次的技巧，两者相似而又不同。

当我们给 DOM 绑定事件的时候，加了防抖和节流的函数变得特别有用。为什么呢？因为我们在事件和函数执行之间加了一个控制层。记住，我们是无法控制 DOM 事件触发频率的。

参考文章 [实例解析防抖动（Debouncing）和节流阀（Throttling）](http://jinlong.github.io/2016/04/24/Debouncing-and-Throttling-Explained-Through-Examples/)


## 防抖函数在react中的使用

> 方式一

```
class Widget extends Component{
  state={text:""}
  debouceEvent(...args){
    this.debounceEvent = debouce(...args);
    return e=>{
      e.persist();
      return this.debounceEvent(e);
    }
  }
  
  handleChange=(e)=>{
    this.setState({
      text:e.target.value
    })
  };
  
  componentWillUnmount(){
    this.handleChange.cancel();
  };
  
  render(){
    <div>
      <input onChange={this.debounceEvent(this.handleChange,500)} />
    </div>
  }
}
```

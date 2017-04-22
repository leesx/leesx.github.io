---
layout:     post
title:      Hello 2017
subtitle:    "\"Hello World, Hello Blog\""
date:       2017-02-04
author:     leesx
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - 生活
---

> “🙉🙉🙉 ”


> 高阶组件实际上只是一个方法，这个方法利用一个现有组件去返回另一个包装它的组件。看一下这个 connectToStores 的执行：

```
function connectToStores(Component, stores, getStateFromStores) {
    const StoreConnection = React.createClass({
        getInitialState() {
            return getStateFromStores(this.props);
        },
        componentDidMount() {
            stores.forEach(store =>
                store.addChangeListener(this.handleStoresChanged)
            );
        },
        componentWillUnmount() {
            stores.forEach(store =>
                store.removeChangeListener(this.handleStoresChanged)
            );
        },
        handleStoresChanged() {
            if (this.isMounted()) {
                this.setState(getStateFromStores(this.props));
            }
        },
        render() {
            return <Component {...this.props} {...this.state} />;
        }
    });
    return StoreConnection;
};
```
> 这看起来和 mixin 非常类似，但是它包装了组件并且传递状态给这个被包装的组件，用这种办法替代管理组件的内在状态。通过简单的组件嵌套，包装组件的生命周期钩子无需任何特殊的合并行为就可以发挥作用。

接下来是这样用的：

```
var ProfilePage = React.createClass({
    propTypes: {
        userId: PropTypes.number.isRequired,
        user: PropTypes.object // note that user is now a prop
    },
    render() {
        var { user } = this.props; // get user from props
        return <div>{user ? user.name : 'Loading'}</div>;
    }
});
// Now wrap ProfilePage using a higher-order component:
ProfilePage = connectToStores(ProfilePage, [UserStore], props => ({
    user: UserStore.get(props.userId)
});
```
这样就 OK 啦！

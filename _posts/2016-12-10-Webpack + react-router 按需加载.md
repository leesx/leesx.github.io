#### 关键函数

require.ensure(dependencies, callback, chunkName)
[详情请见官方文档](https://webpack.github.io/docs/code-splitting.html)

#### webpack 配置
```
output: {
    path: path.join(__dirname, '../dist'),
    filename: '[name].js',
    sourceMapFilename: '[file].map',
    publicPath: '/static/',
    //加这个！
    chunkFilename: '[name].[chunkhash:5].chunk.js',
},
```

name 表示创建的chunk的指定名字，如果未指定则用id作为name
chunkhash 是文件的 hash 码，用来保证文件的唯一性，这里使用前五位（前几位可随意选取）

#### 结合react-router
[详情请见文档](https://react-guide.github.io/react-router-cn/docs/guides/advanced/DynamicRouting.html)
其中提供了三个函数分别是：
- getChildRoutes
- getIndexRoute
- getComponents

他们的作用都是访问到当前路由时才会去加载脚本，由此按需加载便得以实现。
官方的例子中使用的是以对象的形式构建路由，在此，我觉得用jsx更加直观。
所以我们需要将原本Route组件的component属性换成getComponent以此来运行我们的关键函数。

##### 示例代码
```
import React from 'react';
import {
    Route,
    IndexRedirect,
} from 'react-router';
import rootNode from './rootNode';

const routes = (
    <div>
        <Route path='/' component={rootNode}>
            <IndexRedirect to='index' />
            <Route
                path='index'
                getComponent={(location, cb) => {
                                require.ensure([], require => {
                                    cb(null, require('components/Index').default);
                                }, 'index');
                            }} />
        </Route>
    </div>
);

export default routes;
```
这样按需就在就完成了，但是我们可以用更优雅的方式来写。
```
import React from 'react';
import {
    Route,
    IndexRedirect,
} from 'react-router';
import rootNode from './rootNode';

const index  = (location, cb) => {
  require.ensure([], require => {
    cb(null, require('components/Index').default);
  }, 'index');
};

const routes = (
        <Route path='/' component={rootNode}>
            <IndexRedirect to='index' />
            <Route path='index' getComponent={index} />
        </Route>
    );

export default routes;
```
将对应组件的加载函数进行封装，这样更加直观易懂。

##### 注意点

1. require('components/Index').default中require方法的参数不能使用变量，只能使用字符串！

2. 如果你的组件是使用es5的module.exports导出的话，那么只需要require('components/Index')即可。而如果你的组件是使用es6的export default导出的话，那么需要加上default！例如：require('components/Index').default

3. 如果在路由页面使用了按需加载（require.ensure）加载路由级组件的方式，那么在其他地方（包括本页面）就不要再import了，否则不会打包生成chunk文件。简而言之，需要按需加载的路由级组件必须在路由页面进行加载。 

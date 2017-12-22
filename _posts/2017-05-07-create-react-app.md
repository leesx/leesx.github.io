今天想用creat-react-app搭建一个测试发现不支持`css modules` 。
既然不支持那就改呀？

直接全局搜索 `style!css` 然后在webpack配置文件中找到`style!css?importLoaders=1!postcss`
替换为
```
//after
style!css?importLoaders=1&modules&localIdentName=[name]__[local]___[hash:base64:5]!postcss;

```

再找到webpack.config.prod.js文件替换
```
loader: ExtractTextPlugin.extract('style', 'css?importLoaders=1!postcss')
// after
loader: ExtractTextPlugin.extract('style', 'css?importLoaders=1&modules&localIdentName=[name]__[local]___[hash:base64:5]!postcss')
```

ok 运行npm start 测试一下 完美支持～！

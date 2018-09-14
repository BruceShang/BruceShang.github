---
title: 使用vue脚手架vue-cli配置多页面应用
date: 2018-05-05 17:41:22
tags: [webpack, vue]
categories: webpack
---
#### webpack常用插件
> 
1. html-webpack-plugin // 生成一个html文件
2. clean-webpack-plugin // 清除dist文件夹中重复的文件
3. webpack-merger // 合并webpack配置文件
4. webpack-dev-server  // 热启动
5. extract-text-webpack-plguin // 抽取样式文本



### 1.配置多入口
```js
// utils.js
// 查找符合特定规则的文件路径名
var glob = require('glob')
//获取多级的入口文件
exports.getMultiEntry = function (globPath) {
  var entries = {},
    basename, tmp, pathname;

  glob.sync(globPath).forEach(function (entry) {
    basename = path.basename(entry, path.extname(entry));
    tmp = entry.split('/').splice(-4);

    var pathsrc = tmp[0] + '/' + tmp[1];
    if (tmp[0] == 'src') {
      pathsrc = tmp[1];
    }
    pathname = pathsrc + '/' + basename; // 正确输出js和html的路径
    entries[pathname] = entry;

  });

  return entries;

}
```
<!--more-->
### 2. 在webpack.base.conf.js中赋值给entry

```js
const utils = require('./utils')
// 多页面设置入口文件
const entries = utils.getMultiEntry('./src/' + config.moduleName + '/**/**/*.js');
module.exports = {
  entry: entries,
}
```

### 3. 构建生成多页面的HtmlWebpackPlugin配置

```js
// webpack.dev.conf.js webpack.prod.conf.js
分别在webpack.dev.conf.js 和webpack.prod.conf.js中设置
var pages = utils.getMultiEntry('./src/' + config.moduleName + '/**/**/*.html');
for (var pathname in pages) {
  var conf = {
    filename: pathname + '.html',
    template: pages[pathname], // 模板路径
    chunks: ['vendor', pathname], // 每个html引用的js模块
    inject: true,              // js插入位置
    hash: true
  };

  webpackConfig.plugins.push(new HtmlWebpackPlugin(conf));
}

module.exports = webpackConfig
```
### 4. 简单的几步走，具体细节看项目代码
[使用vue脚手架vue-cli配置多页面应用](https://github.com/BruceShang/Vue-Cli-MultiplePage)
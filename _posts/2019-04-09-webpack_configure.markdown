---
layout:     post
title:      "webpack 配置总结"
subtitle:   " \"webpack configure\""
date:      2019-04-09 00:47:45
author:     "xiaobai"
header-style: text
catalog: true
tags:
    - 程序世界
     
---




# webpack
##  devtool
- 生成Source Maps(方便调试)

devtool选项 | 配置结果
---|---
source-map | 在一个单独的文件中产生一个完整且功能完全的文件。这个文件具有最好的source map，但是它会减慢打包速度；
cheap-module-source-map |在一个单独的文件中生成一个不带列映射的map，不带列映射提高了打包速度，但是也使得浏览器开发者工具只能对应到具体的行，不能对应到具体的列（符号），会对调试造成不便；
eval-source-map | 使用eval打包源文件模块，在同一个文件中生成干净的完整的source map。这个选项可以在不影响构建速度的前提下生成完整的sourcemap，但是对打包后输出的JS文件的执行具有性能和安全的隐患。在开发阶段这是一个非常好的选项，在生产阶段则一定不要启用这个选项；
cheap-module-eval-source-map | 这是在打包文件时最快的生成source map的方法，生成的Source Map 会和打包后的JavaScript文件同行显示，没有列映射，和eval-source-map选项具有相似的缺点；

## 使用webpack构建本地服务器

devserver的配置选项 | 功能描述
---|---
contentBase	| 默认webpack-dev-server会为根文件夹提供本地服务器，如果想为另外一个目录下的文件提供本地服务器，应该在这里设置其所在目录（本例设置到“public"目录）
port |	设置默认监听端口，如果省略，默认为”8080“
inline|设置为true，当源文件改变时会自动刷新页面
historyApiFallback	|在开发单页应用时非常有用，它依赖于HTML5 history API，如果设置为true，所有的跳转将指向index.html

## Loaders
- 调用外部脚本或者工具，实现对不同格式文件的处理【scss->css】 es6,es7 -> 浏览器兼容的js文件
- Loaders需要单独安装并且需要在webpack.config.js中的modules关键字下进行配置，Loaders的配置包括以下几方面：
test：一个用以匹配loaders所处理文件的拓展名的正则表达式（必须）
loader：loader的名称（必须）
include/exclude:手动添加必须处理的文件（文件夹）或屏蔽不需要处理的文件（文件夹）（可选）；
query：为loaders提供额外的设置选项（可选）

```
module.exports = {
  mode:"development",
  entry: './main.js',
  output: {
    path:__dirname,
    filename: 'bundle.js'
  },
  module: {
    rules:[
      {
        test: /\.css$/,
        use: [ 'style-loader', 'css-loader' ]
      },
    ]
  }
};

```

## 模块
Webpack有一个不可不说的优点，它把所有的文件都都当做模块处理，JavaScript代码，CSS和fonts以及图片等等通过合适的loader都可以被处理。
### css
webpack提供两个工具处理样式表，css-loader 和 style-loader，二者处理的任务不同，css-loader使你能够使用类似@import 和 url(...)的方法实现 require()的功能,
style-loader将所有的计算后的样式加入页面中，二者组合在一起使你能够把样式表嵌入webpack打包后的JS文件中。

### 插件
- 拓展webpack，在整个构建过程中生效，执行相关任务。
- ==Loaders和Plugins常常被弄混，但是他们其实是完全不同的东西，可以这么来说，loaders是在打包构建过程中用来处理源文件的（JSX，Scss，Less..），一次处理一个，插件并不直接操作单个文件，它直接对整个构建过程起作用。==

```
module.export = {
    plugins: [
        new webpack.BannerPlugin('版权所有，翻版必究')
    ],
}
```
***
常见webpack插件

- HtmlWebpackPlugin（根据一个index.html 模版，生成一个自动引用打包后js的新index.html）

- Hot Module Replacement(当组建中的代码修改之后，可以实时的进行刷新查看修改后的效果)

需要做配置： webpack-dev-server需要配置hot :true

---
优化插件：
UglifyJsPlugin：压缩JS代码；

ExtractTextPlugin：分离CSS和JS文件(webpack4.x 需要更新配套 可用const miniCssExtractPlugin = require('mini-css-extract-plugin'); 替代，配合HtmlWebpackPlugin会将生成的css自动引入html中)

==webpack 4.x 有些方法是已经更新了的。包括node的版本。有些插件也不再支持，而需要新的配置方法 可以参考https://blog.csdn.net/bubbling_coding/article/details/81585412==

### manifest
- 记录所有模块的详细要点（映射关系表）
- 当完成打包并发送到浏览器时，会在运行时通过 Manifest 来解析和加载模块。
- 无论你选择哪种模块语法，那些 import 或 require 语句现在都已经转换为 __webpack_require__ 方法，此方法指向模块标识符(module identifier)。通过使用 manifest 中的数据，runtime 将能够查询模块标识符，检索出背后对应的模块。


### runtime

runtime，以及伴随的 manifest 数据，主要是指：在浏览器运行时，webpack 用来连接模块化的应用程序的所有代码。runtime 包含：在模块交互时，连接模块所需的加载和解析逻辑。包括浏览器中的已加载模块的连接，以及懒加载模块的执行逻辑。【链接模块和解析模块】

runtime 做自己该做的，使用 manifest 来执行其操作

### vendor

第三方库打包 


### resolve
webpack在启动后会从配置的入口模块触发找出所有依赖的模块，Resolve配置webpack如何寻找模块对应的文件。

.alias
resolve.alias配置项通过别名来把原来导入路径映射成一个新的导入路径。如下：
```
resolve: {
    alias: {
        '@': './src/components/'
    }
}
```
alias还支持$符号来缩小范围只命中以关键字结尾的导入语句：

.extensions

在导入语句没带文件后缀时，webpack会自动带上后缀去尝试访问文件是否存在。resolve.extensions用于配置在尝试过程中用到的后缀列表，默认是：

```extensions:['.js', '.json']```

也就是说当遇到require('./data')这样的导入语句时，webpack会先去寻找./data.js文件，如果找不到则去找./data.json文件，如果还是找不到则会报错。












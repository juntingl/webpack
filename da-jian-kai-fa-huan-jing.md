# 搭建开发环境

为什么要在本地搭建一个开发环境？真是开发中，我们的代码是跑在服务器上，搭建本地的开发环境，其实就搭建一个本地web服务器，让本地环境和线上环境更接近，更有利于我们开发和调试。

## 实现的三种方式

* webpack wathc mode     只是监听文件的改动，还是需要搭建服务器
* webpack-dev-server
* express + webpack-dev-middleware

### webpack wathc mode

先介绍一个插件 `clean-webpack-plugin`，为了每次打包后只又本次生成的文件，防止文件夹的文件越来越来多.

[文档](https://www.npmjs.com/package/clean-webpack-plugin)

```js
$ npm i clean-webpack-plugin -D
# webpack.config.js
const CleanWebpackPlugin = require('clean-webpack-plugin');

{
  plugins: [
    new CleanWebpackPlugin(paths [, {options}])
  ]
}

# 启动监听模式

$ webpack watch
// or
$ webpack -w

```

### webpack-dev-server

官方提供的本地开发服务器

#### 功能

* live reloading（浏览器自动刷新）
* 打包文件 ？ NO，不会帮你打包，只是提供本地开发环境的服务 本地开发、本地调试，打包还是需要 通过 webpack 命令来大包的
* 路径重定向
* 支持https
* 浏览器中显示编译错误
* 接口代理
* 模块热更新 （在不刷新浏览器的情况下更新）

#### 配置

```js
# 安装插件
$ npm i webpack-dev-server -D
# webpack.config.js

module.exports = {
    entry: {
        "app": './src/app.js'
    },
    output: {
        path: path.resolve(__dirname, 'dist'),
        publicPath: '/',
        filename: 'js/[name].bundle.js',
        chunkFilename: '[name].chunk.js'
    },

    devServer: {
        inline: false, // 非 inline 模式，地址栏会显示 webpack-dev-server 路径，和打包进度
        port: 9001,
        historyApiFallback: true // 简单使用，不需要写规则; 可以配置rewrites规则
    },
    ...
}

# package.json

"script": {
    "start": "webpack-dev-server --open"    // 开启本地服务器，并打开浏览器
}
```

#### Proxy

* 代理远程接口请求
* `http-proxy-middleware` webpack集成了此第三方插件
  * options
    * target 指定代理地址
    * changeOrigin  改变你的源到选择的 URL, 默认是 false， 开启 true
    * headers 请求头
    * logLevel 输出代理的日志信息
    * pathRewrite
* `devServer.proxy` 这里配置就可以了

```js
# app.js 本地请求某条微博评论接口，模拟代理出去

// $.get('/api/comments/show', {
$.get('/comments/show', {   // 重定向规则
    id: '4193586758833502',
    page: 1
}, function (data) {
    console.log(data)
});

# webpack.config.js

devServer: {
    port: 9001,
    proxy: {
        '/': {  // 重定向规则，把 api 去了
            target: 'https://m.weibo.cn',
            changeOrigin: true,      // 这个不开启，是没法请求成功的
            logLevel: 'debug',       // 输出代理信息
            pathRewrite: {           // 重定向规则
                '^/comments/': 'api/comments/'
            }
        }
    }
}
```

#### Module Hot Reloading

**优点：**

* 保持应用的数据状态
* 节省调试时间
* 样式调试更快

##### 实现

* devServer.hot 设为 true
* webpack.HotModuleReplacementPlugin
* webpack.NamedModulesPlugin  清晰看到模块相对路径

需要一些热更新操作，来触发热更，可以通过 module.hot 监听有无变化，通过 accept 去进行热更新逻辑处理

* module.hot
* module.hot.accept
* module.hot.decline 拒绝热更新

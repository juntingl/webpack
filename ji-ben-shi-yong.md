## 使用 Webpack

1. Webpack 命令
2. Webpack 配置
3. 第三方脚手架（vue-cli、Angular-cli、React-starter）

```
# 帮助
$ webpack -h 
# 查看版本
$ webpack -v
# 打包
$ webpack <entry> [<entry>] <output>
# 启动
$ webpack
// or
$ webpack --config webpack.config.js
```

**Note: 现在安装webpack 基本是安装webpack-cli 工具,还不是很完善**

* 初始化项目的功能
* 版本之间迁移的功能

```
# 交互式初始化一个项目
$ webpack-cli init
# 从webpack v1迁移到v2,<config>是现有webpack配置文件的路径
$ webpack-cli migrate <config> 
```
> 版本迁移知识改变 配置文件，不过安装的依赖不会改变，需要手动去改变

用 Webpack-cli 工具初始化一个项目，指定 webpack 官方提供的一个 webpack 的项目模版

项目初始化

```
$ webpack-cli init
# <指定模版>
$ webpack-cli init webpack-addons-demo 
# 确认是否要使用我们的模版
? Welcome to the demo scaffold! Are you ready? (Use arrow keys)
  Yes
  No
❯  Pengwings
# 你项目的应用入口,默认就好
？What is the entry point in your app?
# common chunk 的名称
? What do you want to name your commonsChunk? vender
```
> 遇到第一个问题时选择 Pengwings 不然 你得到 `webpack.config.js` 就是一个空对象的文件；不过生成的文件里配置也是比较简单的

[webpack-cli文档](https://github.com/webpack/webpack-cli)

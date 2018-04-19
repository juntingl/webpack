## Webpack 是如何诞生的？

先来一段小故事，讲述了 Webpack 作者（Tobias Koppers）是在何种情况下创造了Webpack？

It’s a funny story how I started with webpack. Before getting addicted to JavaScript, I also developed in Java. I tried GWT \(Google Web Toolkit\) in that time. GWT is a Java-to-JavaScript Compiler, which has a great feature:[code splitting](http://www.gwtproject.org/doc/latest/DevGuideCodeSplitting.html). I liked this feature and missed it in existing JavaScript tooling. I made[a pull request](https://github.com/medikoo/modules-webmake/issues/7)to an existing module bundler, but it did not go through. Webpack was born.

开始使用webpack时，先听一个有趣的故事。 在沉迷JavaScript之前，我也是用Java开发的。 那段时间我尝试了GWT（Google Web Toolkit）。 GWT是一个Java-to-JavaScript编译器，它具有很好的特性：代码分割。 我喜欢这个功能，但现有的JavaScript工具中缺没有它。 所以我向现有的模块打包程序提出了请求，但未完成。因此， Webpack诞生了。

文摘: [https://survivejs.com/webpack/foreword/](https://survivejs.com/webpack/foreword/)

## 为什么需要构建？

随着时间，前端变化日新月异、使用场景的增加、开发复杂度的加大、框架去中心化（专注解决某一个问题，其他的由别的依赖工具来解决，不是全面自我解决）、需求的增多、技术升级 导致我们需要一套合理完整的构建工具去简化开发的成本。我们可以从以下几个点出发来进行分析：开发分工的变化、框架的变化、语言的变化、环境的变化、社区的变化、工具的变化

### 开发分工的演进

![](/assets/开发分工.jpg)

由图可以清晰的看出过去的切面仔时代到真正可以称为前端工程师时代，前端所赋予能力的提高、复杂度提高、占具地位的提高，也让推进着我们对工程化构建的需求。

### 框架的演进

![](/assets/框架的演进.jpg)

从众多JS 库，简单的引入库进行进行开发时代，到模块化概念的出现； 不断演进到 MVC 软件架构模式，在然后到 MVVM 架构模式，模块化、组件化核心出发，无疑更需要转换编译。

## 

## 语言的演进

* ES6/7/8 的发布
* Less、Sass、PostCss 的出现

各个 浏览器对新发布的特性兼容性并不好，而且各个浏览器都有自己的前缀，Less、Sass...更是需要预编译；所以更需要构建工具来处理。

## 环境的变化

* node 出现，让js可运行在服务端

* 智能手机、平板 移动端的出现

## 社区的演进

* npm 社区

随着npm社区的出现，项目需要的依赖越来越多，本地管理依赖，让我们管理项目就更方便了

## 工具的变化

* Gulp
* Grunt
* Bower
* Rollup.js
* Yeoman
* Browserify
* Fis
* webpack
* parcel

大量的构建工具的出现，也说明了我们需要构建。

## 为什么选择Webpack？

* 三大主流框架的使用
* Code-splitting （代码分割）
* 模块化的支持
* 拥有比较全面的解决各种问题的能力




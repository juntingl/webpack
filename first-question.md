## Webpack 是如何诞生的？

先来一段小故事，讲述了 Webpack 作者（Tobias Koppers）是在何种情况下创造了Webpack？

It’s a funny story how I started with webpack. Before getting addicted to JavaScript, I also developed in Java. I tried GWT \(Google Web Toolkit\) in that time. GWT is a Java-to-JavaScript Compiler, which has a great feature:[code splitting](http://www.gwtproject.org/doc/latest/DevGuideCodeSplitting.html). I liked this feature and missed it in existing JavaScript tooling. I made[a pull request](https://github.com/medikoo/modules-webmake/issues/7)to an existing module bundler, but it did not go through. Webpack was born.

开始使用webpack时，先听一个有趣的故事。 在沉迷JavaScript之前，我也是用Java开发的。 那段时间我尝试了GWT（Google Web Toolkit）。 GWT是一个Java-to-JavaScript编译器，它具有很好的特性：代码分割。 我喜欢这个功能，但现有的JavaScript工具中缺没有它。 所以我向现有的模块打包程序提出了请求，但未完成。因此， Webpack诞生了。

文摘: [https://survivejs.com/webpack/foreword/](https://survivejs.com/webpack/foreword/)

## 为什么需要构建？

随着时间，前端变化日新月异、使用场景的增加、业务复杂度的加大、需求的增多、技术升级 导致我们需要一套合理完整的构建工具去简化开发的成本。我们可以从以下几个点出发来进行分析：开发分工的变化、框架的变化、语言的变化、环境的变化、社区的变化、工具的变化

### 开发分工的演进

![](/assets/开发分工.jpg)

由图可以清晰的看出过去的切面仔时代到真正可以称为前端工程师时代，前端所赋予能力的提高、复杂度提高、占具地位的提高，也让推进着我们对工程化构建的需求。

### 框架的演进

![](/assets/框架的演进.jpg)

从众多JS 库，简单的引入库进行库进行开发，到模块化概念的出现； 不断演进到 MVC 软件架构模式






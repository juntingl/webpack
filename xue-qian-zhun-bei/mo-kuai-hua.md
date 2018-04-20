# 模块化

模块化是指解决一个复杂问题时自顶向下逐层把系统划分成若干模块的过程，有多种属性，分别反映其内部特性。

我们拿下图的 谷歌Project Ara模块化手机 为例，直观的了解什么是模块化。一部手机是由各个不同功能的模块进行组装而成。

![](/assets/手机模块.jpg)

## JS 模块化

**模块化发展**

* 命名空间
* CommonJS规范（服务端）
* AMD/CMD/UMD 规范
* ES6 module 规范
### 命名空间

刚刚开始产生模块化思想的时候，是以命名空间规范来达到模块化

* 库名.类别名.方法名

```
var NameSpace = {}

NameSpace.type = NameSpace.type || {}
NameSpace.type.method = function () {}

```
以这种命名空间的形式来达到模块化，有很多的弊端，必须要知道完整的路径；团队开发时必须提前先声明定义好各自的命名空间，以防止覆盖；覆盖了也很难检测出问题

### CommonJS

首先你需要知道：
1. CommonJS 是一套规范
2. CommonJS 是为了解决 JavaScript 的作用域问题而定义的模块形式，可以使每个模块它自身的命名空间中执行。
3. CommonJS 是以在浏览器环境之外构建 JavaScript 生态系统为目标而产生的项目，比如在服务器和桌面环境中。

其次，CommonJS 模块化规范的几个准则：
* 一个文件为一个模块
* 通过 `module.exports` 暴露模块接口
* 通过 `require` 引入模块
* 运行在服务端，是同步加载

``` 
# math.js 暴露模块
function add () {
    var sum = 0, i = 0, args = arguments, l = args.length;
    while (i < l) {
        sum += args[i++];
    }
    return sum;
};
module.exports = aa;

# 引入模块
var math = require('math');
math.add();

var add = require('math').add;

var { add } = require('math');

```

> [文档](http://wiki.commonjs.org/wiki/Modules/1.1.1)

### AMD (Asynchronous Module Definition)

异步模块定义规范（AMD）制定了定义模块的规则，这样模块和模块的依赖可以被异步加载。这和浏览器的异步加载模块的环境刚好适应（浏览器同步加载模块会导致性能、可用性、调试和跨域访问等问题）。

* 使用 `define` 定义模块
* 使用 `require` 加载模块
* 特点： 依赖前置，提前加载（但是是异步，不会影响html和css 的加载）
* 常用的库： RequireJS

```
define(
    // 模块名 （可选）
    "alpha", 
    // 依赖 （可选）
    ["require", "exports", "beta"],
    // 模块输出
    function (require, exports, beta) {
        exports.verb = function() {
            return beta.verb();
            //Or:
            return require("beta").verb();
        }
    }
);

define(function(require) {
    var $ = require('jquery');
    $('body').text('hello world');
});

```
> [文档](https://github.com/amdjs/amdjs-api/wiki/AMD-(%E4%B8%AD%E6%96%87%E7%89%88))

### CMD （Common Module Definition）

* 一个文件为一个模块
* 使用 `define` 来定义一个模块
* 使用 `require` 来加载一个模块
* 特点：懒加载执行 (require 进来，文件下载了，但并不会去执行，需要直到运行到调用的地方才会去执行)

```
// 定义模块
define(function(require, exports, module) {
  // 引入模块
  var math = require('./math');

  // 对外提供模块
  exports.add = function() {
    var sum = 0, i = 0, args = arguments, l = args.length;
    while (i < l) {
      sum += args[i++];
    }
    return sum;
  };

  // 或者 通过 module.exports 提供接口
  module.exports = {}

});


```
> [文档](https://github.com/cmdjs/specification/blob/master/draft/module.md)

### UMD （Universal Module Definition）

UMD模式通常会尝试提供当前最流行的脚本加载器（例如RequireJS等）的兼容性。在很多情况下，它使用AMD作为基础，并添加了特殊外壳来处理CommonJS兼容性。

* 通用模块解决方案
* 三个步骤
    * 判断是否支持 AMD
    * 判断是否支持 CommonJS
    * 都不支持，定义为全局变量

``` UMD 代码
(function (root, factory) {
    if (typeof define === 'function' && define.amd) {
        // AMD. Register as an anonymous module.
        define(['b'], factory);
    } else if (typeof module === 'object' && module.exports) {
        // Node. Does not work with strict CommonJS, but
        // only CommonJS-like environments that support module.exports,
        // like Node.
        module.exports = factory(require('b'));
    } else {
        // Browser globals (root is window)
        root.returnExports = factory(root.b);
    }
}(typeof self !== 'undefined' ? self : this, function (b) {
    // Use b in some fashion.

    // Just return a value to define the module export.
    // This example returns an object, but the module
    // can return a function as the exported value.
    return {};
}));
```

> [文档](https://github.com/umdjs/umd)

### ESM (ECMAScript Module)

* ES6 提出
* 一个文件一个模块
* export / import 暴露接口/引入接口

```ES6模块
# 引入 import
import { stat, exists, readFile } from 'fs';
import stat from './stat';
// 引入时重命名
import stat as start from './stat';
// 将mylib模块导出的方法全部引入到 mylib 中
import  * as mylib from './mylib';



# 导出 export
var firstName = 'Michael';
var lastName = 'Jackson';
var year = 1958;

export {firstName, lastName, year};

export function multiply(x, y) {
  return x * y;
};

// 写法一
export var m = 1;

// 写法二
var m = 1;
export {m};

// 写法三
var n = 1;
export {n as m};

export class myClass {}

export default 123;

export default funtion (x) {
    return x * 2;
};

```


# 处理CSS

1. webpack引入css
2. CSS modules
3. 配置 less / sass
4. 提取 CSS 代码 （不提取，就会打包到js里）

## 引入CSS

两者结合使用：

* style-loader   载入页面中创建 <style> 标签将CSS添加到DOM
    * style-loader/url  需要配合 file-loader，载入页面中是已<link> 标签将css添加到DOM,但是它是有几个css文件就有几个link，不怎么使用这种方式
    * style-loader/useable
* css-loader     让 JS文件中可以引入 css，解析css

**简单的🌰**

安照以下目录格式初始化：

```
.
├── index.html
├── package.json
├── src
│   ├── app.js
│   └── css
│       ├── base.css
│       └── common.css
└── webpack.config.js
```

代码：

```
# index.html, 常规html文件，引入打包好后的js文件

<script src="./dist/app.bundle.js"></script>  

# webpack.config.js
var path = require('path')

module.exports = {
    entry: {
        app: './src/app.js'
    },
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: '[name].bundle.js'
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    {
                        loader: 'style-loader'
                    },
                    {
                        loader: 'css-loader'
                    }
                ]
            }
        ]
    }
}

# app.js
import './css/base.css'

# base.css
html {
    background: yellowgreen;
}
```

**style-loader/url 怎么使用**

* 需要配合着 `file-loader`
* 引入几个css， link 就有几个，不推荐使用

```
var path = require('path')

module.exports = {
    entry: {
        app: './src/app.js'
    },
    output: {
        path: path.resolve(__dirname, 'dist'),
        publicPath: './dist/',          // 配置静态资源文件路径
        filename: '[name].bundle.js'
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    {
                        loader: 'style-loader/url'
                    },
                    {
                        loader: 'file-loader'   // 更改
                    }
                ]
            }
        ]
    }
}
```

**style-loader/useable 怎么使用**

* 控制样式插入或不插入页面中
* 打包以后也可以在浏览器运行环境中运行，控制样式的插入

```
# app.js
import base from './css/base.css'
import common from './css/common.css'

var flag = false;

setInterval(function () {
    if (flag) {
        base.unuse()
    } else {
        base.use()
    }
    flag = !flag
}, 500)

# webpack.config.js 修改

rules: [
    {
        test: /\.css$/,
        use: [
            {
                loader: 'style-loader/useable'
            },
            {
                loader: 'css-loader'
                // loader: 'file-loader'
            }
        ]
    }
]
```
### style-loader 的options配置

* inserAt (style标签插入位置)
* insertInfo (插入到dom)
* singleton （是否只使用一个style标签）
* transform （转化，浏览器环境下，插入页面前）

```
# 新建一个 css.transform.js
module.exports = function (css) {
    // 并不是打包的时候运行的，运行webpack 的时候不会运行
    // 要在 style-loader 塞入样式文件到 html 页面执行的
    // 运行环境是在浏览器下执行的，可以拿到 浏览器相关参数
    // import 了几次css文件，就会执行几次
    console.log(css);
    console.log(window.innerWidth)
    console.log(window.innerHeight);

    if (window.innerWidth >= 768) {
        return css.replace('yellowgreen', 'red')
    } else {
        return css.replace('yellowgreen', 'orange')
    }
}

# webpack.js
var path = require('path')

module.exports = {
    entry: {
        app: './src/app.js'
    },
    output: {
        path: path.resolve(__dirname, 'dist'),
        publicPath: './dist/',
        filename: '[name].bundle.js'
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    {
                        loader: 'style-loader',
                        options: {
                            insertInto: '#app',
                            singleton: true,
                            transform: './src/css.transform.js'
                        }
                    },
                    {
                        loader: 'css-loader'
                        // loader: 'file-loader'
                    }
                ]
            }
        ]
    }
}
```

### css-loader 的options配置

* alias (解析的别名)
* importLoader (@import)
* Minimize (是否压缩)
* module (启用css-modules)

**CSS-Modules模块化的一些知识点**

* :local    (局部样式)
* :global   （全局样式） 
* composes  （继承一段样式）
* composes ... from path (引入一段样式)
* localIdentName: '[path][name]_[local]--[hash:base64:5]' （定义编译后 class 名称格式）
    * path 引用 css 路径
    * name 当前 import 的 css 名称
    * local 本地的样式 class 的名称
    * hash 加盐 防止有重复

> 注意： 使用 composes 必须在其他规则之前，开头第一行使用。不然就会影响css 文件的加载顺序。


**继续使用上面简单的🌰**

```
# webpack.config.js 修改后

module: {
    rules: [
        {
            test: /\.css$/,
            use: [
                {
                    loader: 'style-loader',
                    options: {
                        // insertInto: '#app', 注释，因为后面对 app 样式覆盖
                        singleton: true,
                        transform: './src/css.transform.js'
                    }
                },
                {
                    loader: 'css-loader',
                    options: {
                        // minimize: true,  // 压缩
                        module: true,       // 启用 css-modules
                        localIdentName: '[path][name]_[local][hash:base64:5]'  // 根据文件路径+文件名_+本地样式名+一串加盐生成的类名，更清晰直观
                    }
                }
            ]
        }
    ]
}
```

## 配置 Less / Sass

**安装相关依赖**

```
$ npm install less-loader less --save-dev
$ npm install sass-loader node-sass --save-dev
```

**less/sass 配置也是相当的简单**

1. 如果只存在 less / sass 的样式文件，只需要在use里的 `style-loader->css-loader->` 加上 `less-loader`就可以了
2. CSS loader 的处理是从后向前依次处理，`less-loader` 处理完交给`css-loader`处理，`css-loader`处理完，交给 `style-loader`处理； 所以顺序很重要
3. 如果存在多样式的文件，就要新增一个 rules 规则配置项了

```
# webpack.config.js

    module: {
        rules: [
            {
                test: /\.less$/,
                use: [
                    {
                        loader: 'style-loader',
                        options: {
                            // insertInto: '#app',
                            singleton: true,
                            transform: './src/css.transform.js'
                        }
                    },
                    {
                        loader: 'css-loader',
                        options: {
                            // minimize: true,
                            module: true,
                            localIdentName: '[path][name]_[local][hash:base64:5]'
                        }
                    },
                    {
                        loader: 'less-loader'
                    }
                ]
            }
        ]
    }
```

## 提取CSS

* ExtractTextWebpackPlugin （主流）
* extract-loader

**需要安装插件**

```
$ npm install extract-text-webpack-plugin --save-dev
```

### 小🌰子

还是使用上面的例子进下改造：

1. 下载依赖
2. `src` 下新增`components` 组建目录， 目录下新增 `a.js`，文件中引入 `css/components/a.less` 样式文件
3. `app.js` 使用动态函数载入组件 `a.js`
3. 指定 动态打包 chunk 的名字 

```
# webpack.config.js

var path = require('path')
var ExtractTextWebpackPlugin = require('extract-text-webpack-plugin');

module.exports = {
    entry: {
        app: './src/app.js'
    },
    output: {
        path: path.resolve(__dirname, 'dist'),
        publicPath: './dist/',
        filename: '[name].bundle.js',       // 初始化打包的名称
        chunkFilename: '[name].bundle.js'   // 动态异步加载打包的名称
    },
    module: {
        rules: [
            {
                test: /\.less$/,
                // 加载loader时，使用此插件来进下， fallback 如何 载入页面， use 使用 loader
                use: ExtractTextWebpackPlugin.extract({
                    fallback: {
                        loader: 'style-loader',
           
                        options: {
                            // insertInto: '#app',
                            singleton: true,
                            transform: './src/css.transform.js'
                        }
                    },
                    use: [
                        {
                            loader: 'css-loader',
                            options: {
                                // minimize: true,
                                module: true,
                                localIdentName: '[path][name]_[local][hash:base64:5]'
                            }
                        },
                        {
                            loader: 'less-loader'
                        }
                    ]
                })
            }
        ]
    },
    plugins: [
        new ExtractTextWebpackPlugin({
            filename: '[name].min.css',
            allChunks: false   // 指定范围
        })
    ]
}

# app.js

import base from './css/base.less'
import common from './css/common.less'

var app = document.getElementById('app');
app.innerHTML = `<div class="${base.box}"></div>`;

import(/*
    webpackChunkName: 'a'    
*/ './components/a').then(function (a) {
    console.log(a);
})

```

## PostCSS in Webpack

**PostCSS相关流行插件了解**

* AutopreFixer
* CSS-nano
* CSS-next

**PostCSS 是什么？**

A tool for transforming CSS with JavaScript.
PostCSS是一个用 JavaScript 工具和插件转换 CSS 代码的工具。

**webpack中如何使用安装**

* postcss
* postcss-loader    
* Autoprefixer      （自动生成各个浏览器的前缀）
* postcss-cssnano   （优化CSS, css-loader 的 minimize为true也是使用css-nano的压缩）
* postcss-cssnext    (支持新语法，例如： CSS Variables、custom selectors(自定义选择器)、calc() 动态计算)

**postcss 其他的插件**
* postcss-import
* postcss-url
* postcss-assets

> 注意：postcss-loader 必须在 css-loader后面，预编译 less/sass-loader前面

```
# 安装各项依赖
$ npm install postcss postcss-loader postcss-cssnext postcss-cssnano autoprefixer --save-dev

# 添加 postcss loader，webpack.config.js

use: [
    {
        loader: 'css-loader',
        options: {
            // minimize: true,
            module: true,
            localIdentName: '[path][name]_[local][hash:base64:5]'
        }
    },
    {
        loader: 'postcss-loader',
        options: {
            ident: 'postcss',
            plugins: [
                // require('autoprefixer')(),   // 前缀
                require('postcss-cssnext')(),   // 里面包含了主动加前缀的功能就不需要上面的插件了， cssnext 可以使用新语法
            ]
        }
    },
    {
        loader: 'less-loader'
    }
]

# common.less 新语法变量
:root {
    --mainColor: red;
}

a {
    color: var(--mainColor)
}
```

**Broswerslist**

兼容各种浏览器

* 所有创建都共用一份配置
    * package.json; 在package.json添加配置，插件都会在此文件里找
    * .browserslistrc; 根目录下创建配置文件

```
# package.json

"browserslist": [
    ">= 1%",
    "last 2 versions"
  ]
}
```


## Tree Shaking

webpack 2.0 加的功能，`Tree Shaking` 意思就是摇树，然后树上的枯叶就会掉下来；那在项目其实也是一个道理，项目中有些代码从来没有用到，还会耽误加载资源的时间，所以 Tree Shaking 很有必要。

**分两种：**

* Js Tree Shaking
* CSS Tree Shaking

**场景**

* 常规优化
* 引入第三方库的某一个功能

### JS Tree Shaking

2版本以后，webpack 已经在打包过程中，会把没有用到的代码标示出来，通过 webpack 提供的插件（`Webpack.otpimize.UglifyJsPlugin`）把废弃代码给移除。

#### 如何标示呢？

打包后，每个代码块开始的地方会已注释的方式进行标示，看下面代码：

```
# 使用到的
/* harmony export (immutable) */ __webpack_exports__["a"] = a;
# 未使用
/* unused harmony export b */
/* unused harmony export c */
```

**安装相关依赖**

```
$ npm install webpack@3.10.0 style-loader css-loader less-loader less extract-text-webpack-plugin --save-dev
```

**先生成下目录结构：**

```
.
├── dist
│   ├── app.bundle.js
│   └── app.min.css
├── index.html
├── package-lock.json
├── package.json
├── src
│   ├── app.js
│   ├── components
│   │   └── utils.js
│   └── css
│       └── base.less
└── webpack.config.js
```

**webpack.config.js**

只有一些css的基本配置，单独提取 css 为一个文件

```
var path = require('path');
var ExtractTextWebpackPlugin = require('extract-text-webpack-plugin');

module.exports = {
    entry: {
        "app": './src/app.js'
    },
    output: {
        path: path.resolve(__dirname, 'dist'),
        publicPath: './dist/',
        filename: '[name].bundle.js',
        chunkFilename: '[name].chunk.js'
    },
    module: {
        rules: [
            {
                test: /\.less$/,
                use: ExtractTextWebpackPlugin.extract({
                    fallback: {
                        loader: 'style-loader',
                        options: { singleton: true }
                    },
                    use: [
                        {
                            loader: 'css-loader'
                        },
                        {
                            loader: 'less-loader'
                        }
                    ]
                })
            }
        ]
    },
    plugins: [
        new ExtractTextWebpackPlugin({
            filename: '[name].min.css',
            allChunks: false   // 指定范围
        })
    ]
}
```

**app.js**

```
import base from './css/base.less';
import { a } from './components/utils';

var app = document.getElementById('app');
app.innerHTML = `<div class="${base.box}"></div>`;

console.log(a());
```

**utils.js**

```
export function a () {
    return 'this is a';
}
export function b () {
    return 'this is b';
}
export function c () {
    return 'this is c';
}
```

**base.less**

```
@homecolor: #ff3333;

html {
    background: @homecolor,
}

.box {
    width: 300px;
    height: 300px;
    border-radius: 4px;
    background: #333;
}
```

**index.html**

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Tree Shaking</title>
</head>
<body>
    <div id="app"></div>
<script src="./dist/app.bundle.js"></script>
</body>
</html>
```

**打包生成后的文件**

```
/* 2 */
/***/ (function(module, __webpack_exports__, __webpack_require__) {

"use strict";
/* harmony export (immutable) */ __webpack_exports__["a"] = a;
/* unused harmony export b */
/* unused harmony export c */
function a () {
    return 'this is a';
}
function b () {
    return 'this is b';
}
function c () {
    return 'this is c';
}

/***/ })
/******/ ]);
```

**使用webpack自带的插件进行 tree shaking**

```
var path = require('path');
var Webpack = require('webpack');
var ExtractTextWebpackPlugin = require('extract-text-webpack-plugin');

module.exports = {
    entry: {
        "app": './src/app.js'
    },
    output: {
        path: path.resolve(__dirname, 'dist'),
        publicPath: './dist/',
        filename: '[name].bundle.js',
        chunkFilename: '[name].chunk.js'
    },
    module: {
        rules: [
            {
                test: /\.less$/,
                use: ExtractTextWebpackPlugin.extract({
                    fallback: {
                        loader: 'style-loader',
                        options: { singleton: true }
                    },
                    use: [
                        {
                            loader: 'css-loader'
                        },
                        {
                            loader: 'less-loader'
                        }
                    ]
                })
            }
        ]
    },
    plugins: [
        new ExtractTextWebpackPlugin({
            filename: '[name].min.css',
            allChunks: false   // 指定范围
        }),
        // JS tree shaking --- 使用这一句就好
        new Webpack.optimize.UglifyJsPlugin()
    ]
}
```

**引用第三方库 lodash，进行 tree shaking**

* lodash        not-working
* lodash-es     not-working
* babel-plugin-lodash   woking

```
# app.js 增加两句，前提 你先安装好依赖
import { chunk } from 'lodash-es';

console.log(chunk([1,2,3,4,5,6,7], 2));
```

然后进行打包后，你会发现文件还是这么大，查看 lodash 源码，因为本身 lodash 就不是模块化的导出方法的所以打包还是这么大, 所以有第三方库本身的原因让 webpack 很难做到 Tree Shaking。

那这么办呢，针对 `lodash`，我们这里可以使用 `babel-plugin-lodash` 来进行解决，`babel-loader`的相关依赖务必安装好，我们来配置下规则，针对 js 文件使用 babel 进行编译

```
# webpack.config.js

{
    test: /\.js$/,
    use: [
        {
            loader: 'babel-loader',
            options: {
                presets: ['env'],
                plugins: ['lodash']
            }
        }
    ]
}
```
### CSS Tree Shaking

**使用工具**

* [Purify CSS](https://github.com/purifycss/purifycss)
* purifycss-webpack （最新的）

> 注意： CSS Tree Shaking 不能和 CSS Module 一块使用，需要一起使用，需要通过一些配置，设置白名单来实现。

**options**

* paths: glob.sync([]) 路径（glob.sync([]) 同时加载更多的工具）
    * glob.sync([]) （`npm install glob-all --save-dev`)


**通过上面的例子来实现下 `CSS Tree Shaking`**


```
# 安装依赖
$ npm install purifycss-webpack purify-css glob-all --save-dev

# webpack.config.js
// 引入插件
var PurifyCSS = require('purifycss-webpack');
var glob = require('glob-all');

// plugins 配置
plugins: [
        new ExtractTextWebpackPlugin({
            filename: '[name].min.css',
            allChunks: false   // 指定范围
        }),
        // PurifyCSS可以配合ExtractTextWebpackPlugin 一起使用的
        // 前提需要放在 ExtractTextWebpackPlugin 后面
        new PurifyCSS({
            paths: glob.sync([
                path.join(__dirname, './*.html'),
                path.join(__dirname, './src/*.js')
            ])
        }),
        // JS tree shaking 
        new Webpack.optimize.UglifyJsPlugin()
    ]

# base.less
@homecolor: #ff3333;

html {
    background: @homecolor,
}

.box {
    width: 300px;
    height: 300px;
    border-radius: 4px;
    background: #333;
}

// 新增三个没有用到的样式
.bigBox {
    width: 400px;
    height: 500px;
    border: 5px;
}

.smallBox {
    width: 400px;
    height: 500px;
    border: 5px;
}

.littleBox {
    width: 400px;
    height: 500px;
    border: 5px;
}
```
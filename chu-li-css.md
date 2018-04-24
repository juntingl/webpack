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
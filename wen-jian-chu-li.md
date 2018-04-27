# 文件处理

* 图片处理
* 字体文件
* 第三方JS库

## 图片处理

**使用场景**

* CSS中引入的图片 （file-loader）
* 自动合成雪碧图   (postcss-sprites)
* 压缩图片        (img-loader)
* Base64编码      (url-loader)

**相关配置参数**

```javascript
# webpack.config.js

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
                        loader: 'postcss-loader',
                        options: {
                            ident: 'postcss',
                            plugins: [
                                // 处理雪碧图
                                require('postcss-sprites')({
                                    // 指定生成的雪碧图放置的位置
                                    spritePath: 'dist/assets/imgs',
                                    retina: true // retina 屏幕处理
                                    // 这里需要注意，文件名需要添加 @2x 来声明是处理 retina屏 的图
                                }),
                                require('postcss-cssnext')()
                            ]
                        }
                    },
                    {
                        loader: 'less-loader'
                    }
                ]
            })
        },
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
        },
        {
            // 对图片文件的处理
            test: /\.(png|jpg|jpeg|gif)$/,
            use: [
                // 引入文件
                // {
                //     loader: 'file-loader',
                //     options: {
                //         publicPath: './assets/imgs/',
                //         // output: 'dist/',
                //         useRelativePath: true
                //     }
                // }
                // url 转换base64，如果设定的 limit 大小偏差太大，就会直接转换成图片，所以使用了 url-loader 可以不需要使用 file-loader了
                {
                    loader: 'url-loader',
                    options: {
                        limit: 100000,  // 文件大小限制
                        publicPath: './assets/imgs/',
                        useRelativePath: true
                    }
                },
                // 图片压缩
                {
                    loader: 'img-loader',
                    options: {
                        pngquant: {
                            quality: 80
                        }
                    }
                }
            ]
        }
    ]
},

```

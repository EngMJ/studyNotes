# webpack小记

## enrty
> [enrty](https://www.webpackjs.com/configuration/entry-context/)

```
entry:{
    'app': './src/app.js',
    'vendor': ['lodash']
}
```

## output
> [output](https://www.webpackjs.com/configuration/output/)
```
let path = require('path')
// ...
output: {
    path: path.resolve(__dirname, 'dist'), // 输出打包文件路径
    publicPath: './dist/', // 打包完成后,文件引用路径
    filename: '[name].bundle.js' // 打包文件名
}
```

## optimizi
> [commonChunkPlugins](https://www.webpackjs.com/plugins/commons-chunk-plugin/)

```
plugins: [
    // 打包业务公共模块
    new webpack.optimize.commonsChunkPlugins({
        name: 'commonFileName', // 打包文件名
        minChunks: 2, // 触发打包最小引用条件
        chunks: ['pageA', 'pageB'] // 指定范围提取公共代码
    }),
    // 打包第三方引用&打包webpack公用代码
    new webpack.optimize.commonsChunkPlugins({
        names: ['vendor', 'manifest'], // 打包文件名
        minChunks: Infinity, // 触发打包最小引用条件指定范围提取公共代码
    })
}
```
**注意点**
* 1 只打包多个entry的common块
* 2 name可对应entry的key值进行通用chunk打包
* 3 names是排序执行

## codeSplit(代码分割) & lazyLoade(懒加载)
> [codeSplit](https://www.webpackjs.com/guides/code-splitting/)

> [lazyLoade](https://www.webpackjs.com/guides/lazy-loading/)

**常用场景**
* 分离业务代码 & 第三方依赖
* 分离业务代码 & 业务公共代码 & 第三方依赖
* 分离首次加载 & 访问后加载的代码
```
    // xxxx.js
    var page = 'pageA'
    if (page === 'pageA') {
      import(/* webpackChunkName: 'pageA' */'./pageA').then((pageA) => {
        console.log(pageA)
      })
    } else if (page === 'pageB') {
      import(/* webpackChunkName: 'pageB' */'./pageB').then((pageB) => {
        console.log(pageB)
      })
    }

    // webpack.config.js
    plugins: [
        new webpack.optimize.CommonsChunkPlugin({
            async: 'async-common', // 异步通用包名称&并开启异步加载
            children: true, // 查询子依赖
            minChunks: 2
        }),

        new webpack.optimize.CommonsChunkPlugin({
            names: ['vendor', 'manifest'], //
            minChunks: infinity //
        })
    ]
```
**注意**
* import同步引入
* /* webpackChunkName: 'chunkName' */使用魔法注释,为分割代码chunk命名

## CSS处理
> **loader**
* 单个loader的use执行顺序: 倒序执行

**css-loader**
* 作用: 包装css文件,能import样式文件到JS中

**style-loader**
* 作用: 创建style标签,并载入打包的CSS
```
    // webpack.config.js
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    {
                        loader: 'style-loader',
                        options: {
                            insertInto: '#app', // 插入到页面对应query条件的DOM中
                            singleton: true, // 是否只打包成单个style标签
                            transform: './css.tranform.js' // 对应js文件路径
                        }
                    },
                    {
                        loader: 'file-loader'
                    }
                ]
            }
        ]
    }
```

```
// css.transform.js
/*
* @prams
   css 代表正在处理的css文本
**/
module.exports = function (css) {
    // 可获取window对象,进行对应处理
    return css
}
```
* 作用: 获取当前浏览器或其他状态信息,对css进行处理
* 注意: 需使用CMD风格输出

**style-loader/url**
* 作用: 配合file-loader实现css文件的link标签引用
* 注意: 多个css,则会出现多个link标签
```
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [
                    {
                        loader: 'style-loader/url'
                    },
                    {
                        loader: 'file-loader'
                    }
                ]
            }
        ]
    }
```

**style-loader/useable**
* 作用: 可通过JS控制css样式的应用开关
```
// app.js
import base from './style/base.css'
const useControl = true
if (useControl) {
    base.use() // 应用base.css样式
} else {
    base.unuse() // 移除base.css样式
}
```

```
// webpack.config.js
module: {
    rules: [
        {
            test: /\.css$/,
            use: [
                {
                    loader: 'style-loader/useable'
                },
                {
                    loader: 'file-loader'
                }
            ]
        }
    ]
}

```

## 文件处理
> 图片处理

**postcss-loader**
* 注意: 可配合url-loader/img-loader使用,进行图片转换与图片压缩
* 作用: 合成雪碧图/兼容等等
* 使用雪碧图时,需将引用的图片文件更名为 [name]@2x.[ext]形式
```
// webpack.config.js
module: {
    rules: [
        // ....
        {
            loader: 'postcss-loader',
            options: {
                ident: 'postcss',
                plugins: [
                    require('postcss-sprites')({ // 合成雪碧图
                        spritePath: 'dist/assets/imgs/sprites', // 雪碧图输出路径
                        retina: true // 是否合成@2x图
                    }),
                    require('postcss-cssnext')()
                ]
            }
        }
        // ....
    ]
}
```


**file-loader**
* 作用: 读取文件

```
// webpack.config.js
module: {
    rules: [
        // ...
        {
            test: /\.(png|jpg|jpeg|gif)$/,
            use: [
                {
                    loader: 'file-loader',
                    options: {
                        publicPath: '', // 绝对路径
                        outputPath: 'dist/', // 输出路径
                        useRelativePath: true // 使用相对路径
                    }
                }
            ]
        }
    ]
}

```

**url-loader**
* 作用: 压缩图片转化为base64,不转化为base64时,直接提取文件

```
// webpack.config.js
module: {
    rules: [
        // ...
        {
            test: /\.(png|jpg|jpeg|gif)$/,
            use: [
                {
                    loader: 'url-loader',
                    options: {
                        name: '[name]-[hash:5].[ext]', // 定义文件名与扩展
                        publicPath: '', // 绝对路径
                        outputPath: 'dist/', // 输出路径
                        useRelativePath: true // 使用相对路径
                        limit: 100000 // 小于10KB,转化为base64
                    }
                }
            ]
        }
    ]
}
```

**img-loader**
* 作用: 图片处理loader, 图片压缩, 合成雪碧图

```
// webpack.config.js
module: {
    rules: [
        // ...
        {
            test: /\.(png|jpg|jpeg|gif)$/,
            use: [
                {
                    loader: 'url-loader',
                    options: {
                        publicPath: '', // 绝对路径
                        outputPath: 'dist/', // 输出路径
                        useRelativePath: true // 使用相对路径
                        limit: 100000 // 小于10KB,转化为base64
                    }
                },
                {
                    loader: 'img-loader',
                    options: {
                        pngquant: {
                            quality: 80 // 质量
                        }
                    }
                }
            ]
        }
    ]
}
```

> 字体文件

* 注意: 字体文件压缩进css文件中,需先将字体文件下载至本地

```
// webpack.config.js
module: {
    rules: [
        {
            test: /\.(eot|woff2?|ttf|svg)$/,
            use: [
                {
                    loader: 'url-loader',
                    options: {
                        name: '[name]-[hash:5].[ext]',
                        limit: 100000 // 小于10KB则进行转化
                        publicPath: '', // 绝对路径
                        outputPath: 'dist/', // 输出路径
                        useRelativePath: true // 使用相对路径
                    }
                }
            ]
        }
    ]
}

```

> 第三方JS库

* 作用: 通用模块无需每次都进行引用

**loader**
* import-loader
```
// webpack.config.js
module: {
    rules: [
        {
            test: path.resolve(__dirname, 'src/app.js'), // 明确使用的全局模块
            use: [
                {
                    loader: 'import-loader',
                    options: {
                        $: 'jqeury'
                    }
                }
            ]
        }
    ]
}
```

**plugins**

* providePlugin
* 作用: 全局import/require设置

```
// npm 安装的模块
// webpack.config.js
plugins: [
    // ...
    new webpack.providePlugin({
        $: 'jquery' // key代表被赋值的变量, value代表需引用的第三方模块
    })
]
```

```
// 本地第三个库
// webpack.config.js
resolve: {
    alias: { // 作用webpack打包文件时,指定引用路径别名
        jquery$: path.resolve(__dirname, 'src/libs/jquery')
        // key值带$进行精确的文件匹配
    }
},
plugins: [
    // ....
    new webpack.providePlugin({
        $: 'jquery' // key代表被赋值的变量, value代表需引用的第三方模块/相对路径
    })
]
```

## HTML文件处理

**plugins**
* HtmlWebpackPlugin
```
var HtmlWebpackPlugin = require('HtmlWebpackPlugin')
// webpack.config.js
/*
* @options
*   template 模板文件路径
*   filename 文件名
*   minify 压缩
*   chunks 入口
*   inject 是否将CSS/JS插入对应标签
**/
plugins: [
    new HtmlWebpackPlugin({
        filename: 'index.html',
        template: './index.html', // 模板路径
        chunks: ['app'], // 不指定入口chunks,则引用enthy中所有的内容
        minify: {
            collapseWhitesoace: true // 压缩HTML文件,去除换行符
        }
    })
]
```
**loader**
* html-loader
* 作用: HTML中引入图片

```
module: {
    rules: [
        // ....
        {
            test: /\.html$/,
            use: [
                {
                    loader: 'html-loader',
                    options: {
                        attrs: ['img:src', 'img:data-src'] // 处理['标签:属性']对应的资源
                    }
                }
            ]
        }
    ]
}
```

## HTML配合优化
**loader**

**plugins**

* html-webpack-inline-chunk-plugin
* 作用: 将webpack设置嵌入html页面script标签中
```
// webpack.config.js
var htmlInlineChunkPlugin = require('html-webpack-inline-chunk-plugin')
// ....
module: {
    rules: [
        {
            test: /.\js$/,
            use: [
                {
                    loader: 'babel-loader',
                    options: {
                        presets: ['env']
                    }
                }
            ]
        }
        // ....
    ]
},
plugins: [
    // ...
    new webpack.optimize.commonsChunkPlugins({
        name: 'common',

    }),
    new htmlInlineChunkPlugin({
        inlineChunks: ['common'] // 引用指定的chunk
    })
    // ...
]

```

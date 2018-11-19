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

**file-loader**
* 作用: 读取文件

```

```

**url-loader**
* 作用: 压缩base64

```
// webpack.config.js
module:{
    rules:[
        {
            loader: ''
        },
        {
            loader: ''
        }
    ]
}
```

> plugins
```

```

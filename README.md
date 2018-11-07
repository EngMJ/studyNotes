# webpack小记

## enrty

```
entry:{
    'app': './src/app.js',
    'vendor': ['lodash']
}
```
##   optimizi
> commonChunkPlugins

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

## codeSplit 代码分割
> 常用场景
* 分离业务代码 & 第三方依赖
* 分离业务代码 & 业务公共代码 & 第三方依赖
* 分离首次加载 & 访问后加载的代码
```
    // 没看懂,下次继续看
```
## lazyLoade 懒加载
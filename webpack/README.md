## webpack4
webpack4 是号称零配置的 webpack ,相比之前的配置多出了 `mode . optimization` ,可以说新增的 `optimization` 是 `mode`配置的体现实施者

### `mode` 配置
这个配置项有三个参数： `production | development | none`

如果配置了 `production development` 内置了那些是相同的

- `optimization.removeAvailableModules` : 如果 子模块 和 父模块 都加载了同一个 A模块 的时候，开启这个选项将会告诉 webpack 跳过在 子模块 中对 A模块 的检索，这可以加快打包速度。
- `optimization.removeEmptyChunks` ：webpack 将不会去打包一个空的模块
- `optimization.mergeDuplicateChunks` : 告诉webpack合并一些包含了相同模块的模块
- `optimization.nodeEnv` : 会在 `process.env.NODE_ENV` 中传入当前的 node 环境

`production development`  不相同的点

**`production` 模式下**
- `performance:{hints:"error"....} `  性能相关配置
- `optimization.flagIncludedChunks`  告诉webpack确定和标记块，这些块是其他块的子集，当更大的块已经被加载时，不需要加载这些子集
- `optimization.usedExports`  确定每个模块下被使用的导出
- `optimization.concatenateModules`  告诉 webpack 查找可以安全地连接到单个模块的模块图的片段。取决于优化。
- `optimization.minimize` 使用 `UglifyjsWebpackPlugin` 进行代码压缩，也可以继承压缩的包

**`development 模式`**
- `devtool:eval`  生成 `source map` 的格式选择。
- `cache`  缓存模块，避免在未更改时重建它们。
- `module.unsafeCache`  缓存已解决的依赖项，避免重新解析它们。
- `output.pathinfo`  在 `bundle` 中引入项目所包含模块的注释信息。
- `optimization.providedExports`  在可能的情况下确定每个模块的导出。
- `optimization.splitChunks`  找到 chunk 中共享的模块，取出来生成单独的 chunk。该配置用于代码分割打包，取代了曾经的 `CommonsChunkPlugin` 插件。
- `optimization.runtimeChunk`  为 webpack 运行时代码创建单独的 chunk。
- `optimization.noEmitOnErrors`  编译错误时不写入到输出。
- `optimization.namedModules`  给模块更有意义更方便调试的名称。
- `optimization.namedChunks`  给 `chunk` 更有意义更方便调试的名称。

### webpack的配置中需要注意的一些点
如果需要在webpack中使用ESLint的时候，需要使用自己的`ESLint-loader`, 添加到rules中，当我们在webpack中使用了编译类的loader的时候，需要确保他们的执行顺序：**从下而上**，
```js
module:{
    rules:[
        {
            test:'/\.js$/',
            use:[{laoder: 'bable-loader'},{loader: 'eslint-loader'}]
        }
    ]
}
// 上面的use如果写反了，webpack将检查经过babel编译后的文件
```


#### webpack 开发中的一个错误
> [Vue warn]: You are using the runtime-only build of Vue where the template compiler is not available. Either pre-compile the templates into render functions, or use the compiler-included build.

据错误提示说明： 是项目引入的vue编译版本不对

解决方案： 

1. `build/webpack.base.conf.js` 并设置vue的alias别名，如下：
```js
  resolve: {
    alias: {
      vue: 'vue/dist/vue.esm.js'
    }
  }
```

2. 打开src/main.js修改Vue对象初始化。 原因是，使用 template属性，需要引入带编译器的完整版的vue.esm.js
```js
  new Vue({
    el: '#app',
    router,
    render: h => h(App)
  })
```
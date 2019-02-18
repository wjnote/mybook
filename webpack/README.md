## webpack
### webpack的配置中需要注意的一些点
如果需要在webpack中使用ESLint的时候，需要使用自己的`ESLint-loader`,添加到rules中，当我们在webpack中使用了编译类的loader的时候，需要确保他们的执行顺序：**从下而上**，
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
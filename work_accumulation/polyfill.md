## Polyfill(补丁)方案

我们希望浏览器提供一些特性，但是有些浏览器没能实现，所以我们需要自己实现一段代理，这就是补丁


### 在过去常用的 shim + sham
如果是一个3年+ 的前端，应该会有 shim、sham、 [es5-shim] 和 [es6-shim](https://github.com/es-shims/es6-shim)
- shim 是能用的补丁
- sham 是假的意思，所以sham只是一些假的方法，只能保证使用不出错误，但是不能用，因为有些低端的浏览器确实不能实现一些方法

babel-polyfill.js : 这种方式是在 shim + sham之后的一种方法，是引入包含语言层补丁的 babel-polyfill.js ,这样ES6 、 ES7 的特性就可以随便写，缺点就是包含了所有的补丁，有些事浏览器已经支持的，这样就会造成重复和流量浪费


### 现在基本需要支持哪些浏览器就配置那些
`@babel/preset-env + useBuiltins: entry + targets`
1. 在入口文件中引入补丁文件 `import @babel/polyfill`
2. 然后在配置 .babelrc 添加babel配置文件
```js
{
  "presets":[
    ["@babel/env",{
      useBuiltIns: 'entry',
      targets: {chrome:62}
    }]
  ]
}
// useBuiltIns: entry 的含义是找到入口文件里面引入的 @babel/polyfill 并替换成targets浏览器环境下需要的补丁列表
```
3. 然后打包的时候页面上引入的
```js
// import "@babel/polyfill"  就被转变为下面的内容
import "core-js/modules/es7.string.pad-start";
import "core-js/modules/es7.string.pad-end";
```
4. **需要注意的是这个补丁只是基于 core-js 打的补丁，只包含js部分的内容，DOM里的补丁就不在里面**
## babel
- [babel官网](https://babeljs.io/docs/en/)
- [babel中文网站](https://www.babeljs.cn/docs/)
- [babel7升级内容](https://www.w3ctech.com/topic/2150)

babel 自身被分解成了数个核心模块，现在都是插件式的即插即用，还可以在此基础上构建自己的babel插件   可以通过安装 插件（plugins ） 或 预设（presets， 也就是一组插件） 来指示 babel 去做事情
```js
{
  "presets": [],
  "plugins": []     // 如果内容为空，则不能书写该属性
}
```

### 目前babel是版本7
1. 包名升级为 `@babel/core`, 这是babel7的一大调整，原来的  `babel--xx` 包统一迁移到 Babel 域下，域由 @ 符号来标识，一来便于区分官方和非官方的包，二来还可以避免包名的冲突.
2. 到了 babel7，我们就可以完全告别这几个历史预设了: `preset-es2015/es2016/es2017/latest` 使用 `preset-env` 它会自动根据环境加载编译插件，可以设置浏览器和环境

### polyfill
和 stage 预设的结局一样，对于处于建议阶段的特性，polyfill 里面移除了对他们的支持
```js
// 以前的babel-polyfill的实现方式
import 'core-js/shim';
import 'regenerator-runtime/runtime'
```
现在的 @babel/polyfill 就直接引入 `core-js V2`的属于ES正式标准的模块，如果是处于建议阶段的语法特性，就需要手工 `import core-js`中对应的模块

> webpack打包时，一旦匹配到.js文件就使用babel-loader进行处理，babel-loader调用babel-core的api使用bable-preset-env的规则进行转码。
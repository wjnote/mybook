

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



# babel

babel 总共分为三个阶段：解析，转换，生成。

babel 本身不具有任何转化功能，它把转化的功能都分解到一个个 plugin 里面。因此当我们不配置任何插件时，经过 babel 的代码和输入是相同的。

### babel插件： 插件总共分为两种
1. 语法插件： 在解析这一步使用 `babel` 可以解析更多的语法

```js
 function(name1,name2,){}
```
> 上面这样的方式目前来看是错误的，但是有最新的提案允许这种的写法，所以我们可以使用语法插件来转译它

2.  转译插件，将源码转译为 ES5 可以识别的代码


### babel plugin 插件
我们想使用箭头函数，for...of 等，需要使用 `@babel/plugin-transform-arrow-functions`这个插件来转换，


babel 插件一般拆成很小的力度，开发中可以按需引进，例如ES6转ES5的代码 `babel`官方就拆成了20+个插件，这样可以提高性能，提高扩展性，而不需要为了一个功能引入ES6全家桶。


###  babel preset 预设

但是有时候我们单个插件的引入效率低下，想将所有的ES6代码转为ES5的时候，就不适宜使用插件的方式，这里可以使用 `babel preset`， 可以简单的把 `babel preset` 理解为 `babel plugin` 的集合，预先设定的插件列表，比如 `@babel/preset-env` 就包含了所有跟ES6转换相关的插件，还能使用最新的JS标准代码,官方内容包括 `env, react, flow, minify...` 等


### plugin  preset 的执行顺序

项目中可以设置多个 `plugin preset` ,此时，他们的执行顺序非常重要

1. 先执行完所有的plugin，在执行preset
2. 多个plugin，按照声明的顺序执行
3. 多个preset，按照声明次序逆序执行

```js
const foo = ()=>{return <div>this is react template!</div>}
```

上面的代码要需要先执行 jsx 转换为 js 然后在转为 ES5识别的代码，

```js
{
  "plugins": ['trannform-react-jsx'],
  "presets": ['@babel/preset-env']
}
```

简略情况下，插件和 preset 只要列出字符串格式的名字即可。但如果某个 preset 或者插件需要一些配置项(或者说参数)，就需要把自己先变成数组。第一个元素依然是字符串，表示自己的名字；第二个元素是一个对象，即配置对象。



###  需要关注  `bable`中 `polyfill` 的使用
babel可以转化一些新的特性，但是对于新的内置函数（Promise，Set等）静态方法（Array.from, Object.assign）实例方法（Array.prototype.includes）这就需要一个 polyfill 来解决， `babel-polyfill`完全模拟一个ES2015+的环境


使用插件的方式有以下几种（3种方式任选一种，不要混用，有时会出不可知错误）

1. **全局使用`@babel/polyfill`**
    1. 直接在index.html文件中引入polyfill.js的文件或者CDN
    2. 在入口文件顶部直接 `import "@babel/polyfill"`

    > 此方案有点是简单，可以一次性解决所有兼容问题，缺点是一次性引入了ES6+的所有polyfill，打包体积变大，在现代浏览器会造成浪费资源，其次污染了全局对象，已经被抛弃了

2.  **项目中推荐使用preset-env来按需引入polyfill**

```js
module.exports ={
  presets: [
    [
      '@babel/preset-env',
      {
        useBuiltIns: 'usage', // usage: 按需引入  entry: 入口引入(整体引入)  false: 不引入polyfill
        corejs: 2  //表示 corejs的版本  2: corejs@2  3: corejs@3
      }
    ]
  ]
}

const a = Array.from([1]);
// 该方法编译后的文件
"use strict";
require('core-js/modules/es6.string.iterator');
require('core-js/modules/es6.array.from')
var a = Array.from([1]);
```
```shell
corejs 是一个给低版本的浏览器提供接口的库，也是polyfill功能实现的核心，此处指定引入的版本，需要下载响应的版本 `yarn add core-js@2`

此方案适合应用级的开发，babel会根据指定的浏览器兼容列表自动引入所有所需的polyfill
```

3. **`plugin-transform-runtime`也提供了一种runtime的polyfill**

   1. 这里的 corejs 和 presets 里设置的 corejs 是不同的，这个地方指定了一个叫做`runtime-corejs`的版本，使用时也需要安装对应的包 `yarn add @babel/runtime-corejs2`

   2. 这种不会污染全局变量,比较适合类库的开发

   3. 除了实例上的方法`(Array.prototype.includes)` 这样的，其他的内置函数`(Promise,Set,Map)`,静态方法`(Array.from, Object.assign)` 都可以采用这种形式

   4. `plugin-transfrom-runtime`提供的`runtime`形式的polyfill都是这种形式

      

```js
module.exports = {
  plugins: [
    ['@babel/plugin-transform-runtime'], {
      corejs: 2
    }
  ]
}

const a = Array.from([1]);
// babel编译后的结果
var _interopRequireDefault = require("@babel/runtime-corejs2/helpers/interopRequireDefault")
var _from = _interopRequireDefault(require("@babel/runtime-corejs2/core-js/array/from"));
var a = (0, _from['default'])([1]);
// 此方法并没有改变Array.from 而是创建了一个_from来模拟，这样不会污染全局变量
```

---
### babel 配置文件
我们现在主要有三种方式来配置babel的使用 `.babelrc  .babelrc.js  babel.config.js`

前两个配置主要是针对文件夹的，即配置文件所在的文件夹及其子文件夹都会运用这个规则，下层配置会覆盖上层配置， 而`babel.config.js`是针对整个项目的，一个项目只有一个放在根目录下面

注意1：.babelrc文件放置在项目根目录和babel.config.js效果一致，如果两种类型的配置文件都存在，.babelrc会覆盖babel.config.js的配置。
注意2：在package.json里面写配置还是创建配置文件都没有什么区别，看个人习惯。react官方脚手架create-react-app创建的react项目babel配置是写在package.json里面的，而vue官方脚手架@vue/cli创建的vue项目，则是通过babel.config.js设置。
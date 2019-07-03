# babel

babel 总共分为三个阶段：解析，转换，生成。 

babel 本身不具有任何转化功能，它把转化的功能都分解到一个个 plugin 里面。因此当我们不配置任何插件时，经过 babel 的代码和输入是相同的。

### babel插件。插件总共分为两种
1. 语法插件： 在解析这一步是的`babel`可以解析更多的语法

   ```js
   function(name1,name2,){}
   ```

   > 这样的方式是错误的，有最新的提案允许这种的写法，所以我们可以使用语法插件来转译它

2.  转译插件，将源码转译为 ES5 可以识别的代码



### babel plugin 插件

我们想使用箭头函数，for...of 等，需要使用 `transform-es2015-arrow-functions`等插件来转换，



babel 插件一般拆成小的力度，开发中可以按需引进，例如ES6转ES5的代码，`babel`官方就拆成了20+个插件，这样可以提高性能，提高扩展性，而不需要为了一个功能引入ES6全家桶。



###  babel preset 预设

但是有时候我们单个插件的引入效率低下，想将所有的ES6代码转为ES5的时候，就不适宜使用插件的方式，这里可以使用 `babel preset`， 可以简单的把 `babel preset` 理解为 `babel plugin` 的集合，比如 `@babel/preset-env` 就包含了所有跟ES6转换相关的插件，还能使用最新的JS标准代码,官方内容包括`env, react, flow, minify...` 等



### plugin  preset 的执行顺序

项目中可以设置多个 `plugin preset` ,此时，他们的执行顺序非常重要

1. 先执行完所有的plugin，在执行preset
2. 多个plugin，按照声明的顺序执行
3. 多个preset，按照声明次序逆序执行



```js
var profile = <div>
    <img src="avatar.png">
    </div>
var foo = ()=> 'foo'
```

上面的代码要需要先执行 jsx 转换为 js 然后在转为 ES5识别的代码，

```js
{
  "plugins": ['trannform-react-jsx'],
  "presets": ['@babel/preset-env']
}
```





简略情况下，插件和 preset 只要列出字符串格式的名字即可。但如果某个 preset 或者插件需要一些配置项(或者说参数)，就需要把自己先变成数组。第一个元素依然是字符串，表示自己的名字；第二个元素是一个对象，即配置对象。
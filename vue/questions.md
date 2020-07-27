## 一些常见问题列表 极客时间视频
> vue 开发中常见的一些值的思考的问题，底层问题，参考极客时间vue视频的课后习题

#### Q： vue 在子组件中修改了父组件传递的`props`，vue会报错，vue是怎么监听这类修改事件的  ?

#### Q： `this.$emit` 的返回值是什么？ 上层组件 `this.$emit` return了一个值，在组件中能不能接收到 ?
    A： 如果在二级菜单中添加导航的参数，然后使用 props 将组件和路由解耦：

#### Q：父组件向子组件传入的props，在子组件中能赋值到 data 中，后续需要修改的话，是直接修改 `this.data` props会跟随改动吗。
    A： 

#### Q: 为什么不推荐使用index作为 v-for 的key值
    A: 数组中改变了中间的某个元素，列表中它后面的元素的`index`值就变化了，组件`v-for`会对key变化的所有Elements节点都重新渲染，造成没必要的性能开销

#### Q: 组件的事件占用了系统资源，一般在`beforeDestroy`中处理那些操作
    A： 一般在`beforeDestroy`中会清除定时器，一些元素绑定的事件处理函数解除

#### Q：图片合理的优化方式
    A： DOM中存在大量图片的时候会导致加载缓慢的情况，
- 一般开发中会使用 懒加载 的方式加载图片，节点展示的时候才加载图片
- 小图标通过 SVG 或者字体图标的方式
- 通过 base64 或者 webp 的方式加载小型图片
- 能通过cdn加速的大图尽量用cdn

----

#### Q： 相同名称的插槽是合并还是替换(2.6的语法)
    A: 

#### Q： 数组有哪些方法支持响应式更新，如不支持如何处理，底层原理是怎么实现的（push方法会改变原数组）: 如果数组的方法不会改变原数组
    A: vue原生不能检测到数组中的一些方法，返回新数组的那些方法， 可以使用 `arr = arr.concat()`

Vue2内部使用`Object.defineProperty`进行的数据劫持, 而这个API无法探测到对象属性的添加和删除,以及直接给数组下标进行赋值, 而理论上这个API也无法探测到数组的一系列方法(push,splice,pop),  因为这些方法不会改变原数组，而是会返回新数组, 但是Vue框架修改了数组的原型, 使得在调用这些方法修改数据后会执行视图更新的操作
```js
//源码位置:src/core/observer/array.js
methodsToPatch.forEach(function (method) {
  // cache original method
  var original = arrayProto[method];
  def(arrayMethods, method, function mutator () {
    var args = [], len = arguments.length;
    while ( len-- ) args[ len ] = arguments[ len ];

    var result = original.apply(this, args);
    var ob = this.__ob__;
    var inserted;
    switch (method) {
      case 'push':
      case 'unshift':
        inserted = args;
        break
      case 'splice':
        inserted = args.slice(2);
        break
    }
    if (inserted) { ob.observeArray(inserted); }
    // notify change
    ob.dep.notify(); //这一行就会主动调用notify方法,会通知到渲染watcher进行视图更新
    return result
  });
});

// 下面9个方法会修改原数组，在传值的时候传递的是内存指针
// ES5:
a.splice();  a.sort();  a.pop();  a.shift();  a.push();  a.unshift();  a.reverse(); 
// ES6:
a.copyWithin();  a.fill();
```

---

#### Q： vue开发中的临时变量使用 ？ 可以使用函数式组件的方式来实现 ？


#### Q: 页面后退, 数据还原, 滚动条还原，
    A: 


#### Q: 登录超时，获取列表数据，表单提交，


1. 如果父组件向子组件传值，根据该值在 mounted 方法中，执行一个操作，后续的，如果该值变化，但是mounted里面的函数不能再执行 ？


#### Q: 子组件修改父组件的props ，父组件是怎监测到的
    A: 主要是使用了 `Object.defineProperty()` 的方式，主要思想是一个开关变量来监测某个值如果不是父组件修改的，就会报错，


#### Q: 数组有哪些方法支持响应式更新，底层原理实现
    A: 1. 支持的方法： push(), pop(), shift(), unshift(), splice(), sort(), reverse()
      2. 不支持的：filter(), concat(), slice()
      3. 原理同样是使用 `Object.defineProperty` 对数组进行改写


#### Q: vuex是通过什么方式提供响应式数据的？


#### Q： SPA的缺点有哪些，如何解决？
    A： 1. 不利于SEO，搜索引擎优化   2. 首屏渲染时间长

    可以使用服务端渲染 SSR 来解决不利于SEO的问题， 渲染时间长可以使用预渲染 Prerendering

    Nuxt 服务端渲染

---


vue项目的部署
了解将vue集成到完整技术栈中常用设计模式，vue应用程序的数据完整性和安全性

测试： vue团队维护了一个 vue Test Utils的工具 单独测试vue组件

优化： 务器端渲染，也就是在服务器端执行 Vue 应用程序，然后输出 HTML 页面并传给用户。
其他优化手段还包括使用异步组件和渲染函数。

Vue.js 3 将于 2019 年推出，将完全使用 TypeScript 编写

概念过多，记不住。属性、事件、插槽、指令、修饰符等等，开发的时候还要一遍又一遍翻文档。

没能真正理解数据驱动，总是用各种 hack 方法更改视图，为项目挖了一个又一个的坑。

状态管理的不合理，不知道如何组织 Vue 中的 data、provide、vuex、observable。

工程化，大型项目无从下手。我们有单页面、多页面、前后端分离、mock 联调、构建、打包、单测、持续集成等等，想系统掌握很费力。

Vue的框架：构建在 Vue 之上的框架让你无需从头开始实现服务器端渲染

1. Nuxt.js 高性能的 Vue 应用程序，就需要基于组件的路由、服务器端渲染、代码拆分和其他尖端的功能， Nuxt.js 通过各种社区插件提供了这些开箱即用的功能，以及更多的功能选项，如 PWA。
2.  Vuetify Vuetify 在一系列 Vue 组件中实现了 Material Design。因此，你可以使用 Material Design 布局和样式快速构建 Vue 应用程序，以及模态、警报、导航栏、分页等小部件。
3.   NativeScript-Vue  ativeScript 是一个用于在 iOS 和 Android 上使用原生用户界面组件构建应用程序的系统，而 NativeScript-Vue 是一个基于 NativeScript 的框架，提供了 Vue 的语法和组件的使用方式


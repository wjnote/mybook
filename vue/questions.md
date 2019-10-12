## 一些常见问题列表 极客时间视频
> vue 开发中常见的一些值的思考的问题，参考极客时间vue视频的课后思考

如果在二级菜单中添加导航的参数，然后使用 props 将组件和路由解耦：

父组件向子组件传入的props，在子组件中能赋值到 data 中，后续需要修改的话，是直接修改 `this.data` props会跟随改动吗。


vue 在子组件中修改了父组件传递的props，vue会报错，vue是怎么监听这类修改事件的 ？？

`this.$emit` 的返回值是什么？ 上层组件 `this.$emit` return了一个值，在组件中能不能接收到

相同名称的插槽是合并还是替换(2.6的语法)

vue中为什么不能使用index来标识DOM

数组有哪些方法支持响应式更新，如不支持如何处理，底层原理是怎么实现的（push方法会改变原数组）: 如果数组的方法不会改变原数组，可以使用 `arr = arr.concat()` 将改变后的数组再赋值给原数组即可



Vue内部使用Object.defineProperty进行的数据劫持,而这个API无法探测到对象根属性的添加和删除,以及直接给数组下标进行赋值,而理论上这个API也无法探测到数组的一系列方法(push,splice,pop),但是Vue框架修改了数组的原型,使得在调用这些方法修改数据后会执行视图更新的操作，实现代码如下
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
```


```js
let a = [1,2,3];  // 下面9个方法会修改原数组，在传值的时候传递的是内存指针
// ES5:
a.splice();
a.sort();
a.pop();
a.shift();
a.push();
a.unshift();
a.reverse();
// ES6:
a.copyWithin();
a.fill();
```



vue开发中的临时变量使用，？？ 可以使用函数式组件的方式来实现 ？？？

vue开发一个电商的倒计时秒杀组件

> ```
> [Vue warn]: You are using the runtime-only build of Vue where the template compiler is not available. Either pre-compile the templates into render functions, or use the compiler-included build.
> ```

据错误提示说明：**是项目引入的vue编译版本不对**

解决方案：

1. build/webpack.base.conf.js 并设置vue的alias别名，如下：

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


3. 单文件就是个组件的方式 ，然后使用import引入，则不需要完整版的`vue.esm.js`，因为使用`vue-loader`时` *.vue`文件会自动预编译成 JS



`mapState`, `mapGetters`, `mapActions` 和 `mapMutations` 这些函数的作用和在绑定命名空间的的模块时区别


页面后退数据还原，滚动条还原，
登录超时，获取列表数据，表单提交，
多台服务器自动化部署，最终后一个个解决了，


1. 如果父组件向子组件传值，根据该值在 mounted 方法中，执行一个操作，后续的，如果该值变化，但是mounted里面的函数不能再执行 ？


#### 子组件修改父组件的props ，父组件是怎监测到的
主要是使用了 `Object.defineProperty()` 的方式，主要思想是一个开关变量来监测某个值如果不是父组件修改的，就会报错，

#### key循环的时候不能使用index作为值
动态添加的li列表，使用index作为key，然后再删除的时候需要传递当前的index值，很容易引起bug

#### 数组有哪些方法支持响应式更新，底层原理实现

1. 支持的方法： push(), pop(), shift(), unshift(), splice(), sort(), reverse()
2. 不支持的：filter(), concat(), slice()
3. 原理同样是使用 `Object.defineProperty` 对数组进行改写

> 不支持的方法可以使用将改变后的数组赋值给原来数组即可以实现响应更新


#### vuex是通过什么方式提供响应式数据的？


#### SPA的缺点有哪些，如何解决？
1. 不利于SEO，搜索引擎优化
2. 首屏渲染时间长

可以使用服务端渲染 SSR 来解决不利于SEO的问题， 渲染时间长可以使用预渲染 Prerendering

Prerendering 适用于 静态站点，不太变动的页面

Nuxt 服务端渲染
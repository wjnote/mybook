# vue框架

当实例化一个Vue构造函数，会执行 `Vue` 的`init`方法，方法中主要执行三部分内容，这三个部分构成了Vue的整个执行过程

  1. 初始化环境变量
  2. 处理vue组件数据
  3. 解析挂载组件

在初始化的时候，vue将遍历此对象所有属性并采用数据劫持结合发布-订阅模式，通过 `Object.defineProperty`做数据劫持把所有的属性变为 `getter/setter`，在数据变动时发布消息给订阅者，触发响应的监听回调，属于上述的第二个部分。在生命周期`init  created` 之间执行。

<img src="./imgs/001.png" alt="vue响应式原理" style="zoom:80%;" />

`Object.defineProperty` 只能劫持对象的属性， vue2.x 需要对每个对象的每个属性进行遍历，是通过**递归 + 遍历data对象**来实现数据监控的，如果属性也是对象，就需要深度遍历

`Object.defineProperty` 在数组的状态下，将数组下标作为属性的方式监听的话，性能开销会比较大，所以vue2.x没有实现对数组的动态监听

`vue3.x`采用了 `Proxy` 来劫持整个对象，并返回一个新对象，Proxy不仅可以代理对象，也可以代理数组，还可以代理动态增加的属性

```js
// 最简单核心的代码
function defineReactive(obj, key){
  let val = obj[key]
  Object.defineProperty(obj,key,{
    get(){  return val; },
    set(newValue){ val = newValue;}
  })
}
// 完整代码还需要考虑 对象的属性描述符是否可编辑， array的length 属性就不可编辑
// 本来是否设置了 getter setter方法
// 属性的值也是对象，需要递归属性值，爆栈的处理
```


每个组件实例（就是组件）都有对应的 `watcher` 对象，会在渲染的过程中把属性记录为依赖，当依赖的setter调用时，就通知`watcher`重新计算，从而使它关联的组件更新，而不是像 `React` 那样更新该组件及其所有子组件


父组件给子组件传参 prop 的时候，如果是非prop特性的值(即在子组件没有申明对应的接受变量) ，则该类的属性会被写到子组件的根元素上面。



### 异步更新队列
异步更新队列： 在vue中更新DOM并不是同步立马更新的，而是vue开启一个队列，缓冲在同一事件循环中发生的所有数据变化（如果同一个watcher被多次触发，只会推入队列一次），然后在一个事件的 'tick' 中，一次性全部执行更新，在内部使用的是 `Promise.then`,如果想立马使用更新后的数据，就需要触发 `vm.$nextTick()`

```javascript
methods: {
	updata: function(){
		this.message = 'new message'
		this.$nextTick(function(){
		// 现在数据已经更新了 可以使用
		})
	}
}
// 返回的是Promise对象，还可以使用
methods: {
  updata: async function(){
    this.message = 'new message'
    await this.$nextTick();
    // 数据已经更新
  }
}
```

vue 是单向数据流，双向绑定只是一种语法糖，那vue是如何做双向绑定的 ？
`Object.defineProperty` 只是用来做响应式更新的，和双向绑定没有关系，下面是双向绑定的实现原理

```javascript
// 语法糖的形式  可以同时响应多个数据 .sync
<PersonalInfo
	v-model="phoneInfo"
	:zip-code.sync="zipCode"
/>
// 编译之后的实现原理
<PersonalInfo
	:phone-info="phoneInfo"
	@change="val=>(phoneInfo=val)"
	:zip-code="zipCode"
	@update:zipCode="val=>(zipCode=val)"
/>
```


侦听属性（watch）： 一般我们使用计算属性更好，但是如果要访问异步API或者监听router的话，使用侦听属性就会更好

JSX语法：vue可以不用模版直接写 render 渲染函数，使用JSX语法

vue中的插槽有三种：单个插槽、具名插槽、作用域插槽，

### vue 注册组件的方式

> vue中的组件本质就是一个拥有预定义选项的一个vue实例。

1. 全局注册的方式 全局注册的组件可以用在其被注册之后的任何 (通过 `new Vue`) 新创建的 Vue 根实例，也包括其组件树中的所有子组件的模板中。

   ```js
   // 定义名为 todo-item 的新组件 这样全局注册的每个组件名字不能重复
   Vue.component('todo-item', {
     template: '<li>这是个待办项</li>'
   })
   ```

2. 组件的测试方式





自定义组件的循环`v-for`  在组件中使用`v-for`的时候，数据不会被自动传递到组件里，因为组件有自己独立的作用域，为了把数据传递到组件里，我们使用`props`

```js
<my-component
	v-for="(item, index) in items"
	v-bind:item="item"
	v-bind:index="index"
	v-bind:key = "item.id"
/><my-component>
```


vue组件中使用第三方的插件的时候，需要在组件销毁的时候销毁第三方插件的实例，可以在 `beforeDestroy` 声明周期中，还可以使用 **程序化的侦听器** 的方式来销毁插件实例，开发中echarts在组件销毁时就需要注销掉

```js
mounted: function () {
  var picker = new Pikaday({
    field: this.$refs.input,
    format: 'YYYY-MM-DD'
  })

  // 程序化的侦听器
  this.$once('hook:beforeDestroy', function () {
    picker.destroy()
  })
}
```

在写法种，key可以增加我们比较虚拟DOM的性能，更加优化，最好不要使用`index`作为key的值，


### prop传值为对象的时候
我们有时需要修改传入的prop的值，有两种方式
1. 在data中申明变量，将prop设置为其初始值，在`template`中显示该变量，后续就可以修改本组件的值
2. 设置一个计算属性的值，将props的值在其中返回

> 第一种方式的时候，如果在父组件修改了prop的中，在子组件组价的值是不会变化的，使用计算属性的方式的话，子组件的值也会随着父组件的改变而改变

**但是对象和数组的形式例外，即使将prop赋值给data中的变量，然后修改data中的变量，父组件的值也会受到改变，且控制台不会报错，需要注意**

Q: 如果我想让子组件继承prop的值，然后我修改的时候不影响父组件的值，但是父组件的值改变的时候子组件的值也会变化
A: 在组件的data中初始化变量`temp:{...Object}`，修改的时候只修改 `temp` ,再用`watch`监听Object的值，如果改变了再赋值给 temp.


Q: 一个父组件中，引入子组件，传递prop，然后子组件中的方法会执行，父组件中prop改变的时候，子组件生命周期函数不会执行了，methods中的方法会执行吗？
A: 生命周期函数在第一的时候执行，数据改变的时候不会再执行，`ta-model`弹出框的时候 就不能再改变的时候执行方法，需要写在`updated`中


### vue 组件化编程
组件化编程的核心思想应该是关注点分离、单一职责原则和开闭原则。而不是粘贴复制

- 关注点分离：vue本身就很好运用了关注点分离，比如将template、script和style三者分开。在组件化编程还是得运用一下这个思想的。组件的各部分要根据其关注点合理地将其分开，这个分开不一定是分开为两个组件，当业务量比较大的时候，这两者会是组件群，在组件群里面再分离，最小的单元应该会是组件。运用关注点分离之后你会发现原来复杂的问题问题一下子变得简单了，更重要的是维护者可以很容易地接手项目。
- 单一职责原则：很多同学写组件恨不得在一个组件就写完所有东西，生怕多一个组件。单一职责告诉你一个组件，一个函数应该只做一件事。比较官方的说法是一个组件只有一个理由去改动它。独立为一个组件有两种情况：
    1. 一种是这个组件是通用组件，我将通用组件又分为基础组件和通用业务组件，基础组件正是像`elementUI`和`iView`提供的这些组件，这些组件不能跟接口和`vuex`耦合，无论在哪个项目都可以使用；业务通用组件则是通用的业务块，这个组件可以跟接口和vuex耦合，但是它在整个项目中又是通用的。比如有一个输入搜索公司成员的组件，那我将这一块连同接口完整的封装起来并且提供对外接口v-model和一些参数。通用业务组件对整个项目的效率提高有很大帮助。
    2. 另外一种则是纯粹的业务组件，这个业务组件是不可通用的。那为什么要将其独立呢？是为了可维护性和可扩展性，我们将其独立为一个组件之后，业务的关注点也得到了分离。这一种组件独立的要求是其业务高度内聚，只有在业务高度内聚的情况下我们才能很好地把它抽离出来。高度内聚可以理解为这一块的东西跟其它的东西没有关系或者关系很小。

- 开闭原则：对扩展开放对修改关闭，一个好的系统架构应该遵从这个原则。我们写前端组件的时候也不妨多考虑一下这个。多问这样写会有好的扩展性。有时候面对两个或多个很相像的页面，我们一般会把它抽出来，这样做大部分时候是对的，但在需求多变的时候，这会造成麻烦。当业务变更的时候，这两个组件变得不同了，这时候很多人都会选择直接在里面加一下逻辑进行区分，但随着变更的加多，祖传代码在各位手中诞生了。以其留下这种烂代码，在第一次变更的时候就应该将其分开。有时候我们应该在代码量和扩展性之间做一些权衡。

### 事件处理器
vue中使用 `v-on` 监听事件，后面可以直接跟事件处理方法，同时可以向函数中传递多种类型参数，其中  `$event` 是一个特殊变量，表示原始的DOM事件

```vu
<button v-on:click="greet"></button>
<button v-on:click="say('hi')"></button>
<button v-on:click="warn('hi', $event)"></button>

methods:{
	greet(event){}, // event 是原生DOM事件
	say(params){},
	warn(message, event){}
}
```



**动画**
如果你需要使用动画，请了解一下 Vue 的过渡系统，它也是 Vue 核心的一部分。你可以在向 DOM 添加元素或从 DOM 中删除元素时应用动画。

你需要创建 CSS 类来定义所需的动画效果，无论是淡入淡出、更改颜色还是你喜欢的其他方式。当向 DOM 中添加元素或从 DOM 中删除元素时，Vue 会检测到这些变更，并在过渡期间添加或删除相应的 CSS 类。


## watch 响应数据的变化
在Vue2.0中我们可以使用`watch`来监听数据的变动

常规用法
  ```js
  watch:{
    name(newValue, oldValue){},
    'info.gzh': {
      handler(newValue, oldValue){},
      // 配置immediate会在watch之后立即执行
      immediate: true
    },
    params:{
      handler(newValue, oldValue){},
      // 深度监听对象里面的属性 监听对象或者数组内部的属性变动时
      deep: true
    }
  }
  ```

   > 数组（一维、多维）的变化不需要通过深度监听，对象数组中对象的属性变化则需要deep深度监听

Vue实例上的 `$watch` 方法 ，例如我们监听的数据是请求接口返回的数据时

   ```js
    export default{
      methods:{
        loadData(){
          fetch().then(data=>{
            this.formData = data;
            this.$watch('formData', ()=>{/* formData 数据发生变化后进入此回调 */}, {deep: true})
            // this.$watch(()=>this.name, ()=>{ /* 函数的返回值发生变化，进入此回调函数 */ })
          })
        }
      }
    }
   ```

监听函数表达式，除了字符串，`$watch` 的第一个参数也可以是函数，当函数的返回值发生变化时触发回调函数


## 使用Vue注意点
1. 指令的缩写只在其有参数的时候使用

---

##  常用的Vue组件的插件
 -  [Vuelidate](https://github.com/vuelidate/vuelidate) ： 可以实现form表单的验证



工程化，大型项目无从下手。我们有单页面、多页面、前后端分离、mock 联调、构建、打包、单测、持续集成等等，想系统掌握很费力。

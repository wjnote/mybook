# vue框架
在初始化的时候，vue将遍历此对象所有属性，并使用 `Object.defineProperty` 做数据劫持把所有的属性变为 `getter/setter`. 因为 `Object.defineProperty` 在ES5中不能模拟，所以vue不能支持IE8

**指令的缩写只在其有参数的时候使用**

每个组件实例（就是组件）都有对应的 watcher 对象，会在渲染的过程中把属性记录为依赖，当依赖的setter调用时，就通知`watcher`重新计算，从而使它关联的组件更新，而不是想 React 那样更新该组件及其所有子组件

因为vue在实例初始化的时候对属性执行了`getter / settter` ，属性必须在data对象上面才能变为属性依赖，在JS中不能将新添加的属性变为响应式的（`Object.observe` 被废弃了）。 **vue不允许动态添加根级响应式属性，必须在初始化的时候确定好**
```js
<template>
  <div>
    <ul>
      <li
        v-for="(v,i) in list"
        :key="i"
        v-if="v.status === '1'"
      >{{v.text }}</li>
    </ul>
  </div>
</template>
<script>
export default {
  name: "responsive",
  data() {
    return { list: [] };
  },
  method() {
    // vue会劫持data里面的属性，可是list成员内部的属性，也可以触发数据更新
    // 所以在初始化设置的时候需要考虑到所有的数据情况
    setTimeout(() => {
      this.list = [{ text: 1 }, { text: 1 }, { text: 1 }];
    }, 1000);
    setTimeout(() => {
      this.list.forEach((v, i) => {
        v.text = i;
        // 这样的方式是可以实现的，因为在给data设置值的时候
        // vue会判断设置是赋值是否是一个对象，对象的话会对其中的属性也添加数据劫持
      });
      // this.list[i]={text:i}
      // 这样的方式不会生效 数据可以更新但是视图不会
      // 不支持通过索引设置数组成员。
    }, 2000);
    setTimeout(() => {
      this.list.forEach((v, i) => {
        v.text = i;
        v.status = "1";
        // 新增的属性 这样也不会生效
        // vue不能检测到对象属性的增加和删除
      });
    }, 2000);
  }
};
</script>
```

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
	:zip-code.sync="zipcCode"
/>
// 编译之后的实现原理
<PersonalInfo
	:phone-info="phoneInfo"
	@change="val=>(phoneInfo=val)"
	:zip-code="zipCode"
	@update:zipCode="val=>(zipCode=val)"
/>
```



指令： 指令带有前缀 `v-` 他们提供vue的特殊特性 `v-model v-bind v-if v-for v-on`等，每一个都有特殊的功能，指令特性预期是单个JS表达式，指令的职责是表达式的值改变时，将其产生的连带影响，响应式的作用域DOM。指令后面能够接受一个**参数·** 表示的是将该参数与指令表达式的值实现绑定，参数可以使用`[]`的形式来实现JS表达式求值

修饰符： 修饰符是指出指令应该特殊绑定的方式。`.prevent`

计算属性：我们可以在vue实例的 `computed` 中声明计算属性（函数的形式），当作组件的数据一样使用，当计算属性依赖的属性发生改变时，计算属性也会发生更新，默认只有getter，但是需要的话可以设置setter ，设值函数

方法： 可以使用方法来实现计算属性的效果，但是需要理解他们两者的区别和使用场景

1. 计算属性会基于它的响应式依赖进行缓存，依赖不变，则计算属性不会重新计算
2. 方法是每次重新渲染的时候都会重新执行
3. 一般两种实现是一致的，但是有性能开销比较大的时候最好使用计算属性缓存

侦听属性（watch）： 一般我们使用计算属性更好，但是如果要访问异步API或者监听router的话，使用侦听属性就会更好

JSX语法：vue可以不用模版直接写 render 渲染函数，使用JSX语法

文本： 数据绑定使用最常见的 `{{}}` 的形式文件插值， `v-one` 是只能插值一次的指令，其中HTML的文本内容是个特殊的，需要使用 `v-html`指令， 在`{{}}`语法中可以使用JS的代码运算，也会被解析

双大括号 `{{}}` 不能作用在HTML特性上，遇到特性的只能使用各种指令，指令使用 `v-bind:id="XXXX"` 其中的 XXXX 会被当作变量来解析，如果是只想在HTML上设置固定的特性，则不适用指令，直接写属性即可，其中指令的变量中还可以使用JS的运算（每个绑定只能是单个表达式） 

事件也好，插槽也好都是可以通过属性的方式去解决的

vue中的插槽有三种：单个插槽、具名插槽、作用域插槽，



### vue 注册组件的方式

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


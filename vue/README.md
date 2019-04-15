# vue框架
在初始化的时候，vue将遍历此对象所有属性，并使用 `Object.defineProperty` 把所有的属性变为 `getter/setter`. 因为 `Object.defineProperty` 在ES5中不能模拟，所以vue不能支持IE8

每个组件实例（就是组件）都有对应的 watcher 对象，会在渲染的过程中把属性记录为依赖，当依赖的setter调用时，就通知`watcher`重新计算，从而使它关联的组件更新，而不是想 React 那样更新该组件及其所有子组件

因为vue在实例初始化的时候对属性执行了`getter / settter` ，属性必须在data对象上面才能变为属性依赖，在JS中不能将新添加的属性变为响应式的（`Object.observe` 被废弃了）。 **vue不允许动态添加根级响应式属性，必须在初始化的时候确定好**

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
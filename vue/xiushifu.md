## vue 修饰符的使用

修饰符可以同时使用多个，需要注意顺序不同结果可能会不同，**修饰符从左往右判断**

### 表单修饰符

- `.lazy` 作用是在输入框光标离开时才更新视图，而不是输入框修改一个就立马更新
- `.trim` 可以过滤输入中多余的空格，注意只能去除两端的空格，中间的没效果
- `.number` 限制输入数字或者输入的东西转换为数字 ？？（实现好像没有效果）

### 事件修饰符

- `.stop` 阻止事件冒泡，相当于事件中调用了 `event.stopPropagation()`方法
- `.prevent` 阻止事件默认行为，相当于调用了`event.preventDefault()`方法
- `.self` 只当事件是从事件绑定的元素本身触发的时才能触发回调
- `.once` 只能用一次，绑定了事件以后只能触发一次
- `.capture` 修改默认的事件机制，事件触发从包含这个元素的顶层开始往下触发
- `.passive` 监听元素滚动事件的时候，相当于给`onscroll`事件添加了 `.lazy`修饰符
- `.native` 你可能想在某个组件的根元素上监听一个原生事件，可以使用 `v-on`的修饰符 `.native` ,通俗的将就是在父组件中给子组件绑定一个原生的事件，就将子组件变成了普通的HTML标签，不加`.native` 事件时无法触发的（可以理解为该修饰符的作用就是把一个vue组件转化为一个普通的HTML标签，并且该修饰符对普通HTML标签是没有任何作用的。）

```vue
<template>
  <div class="clastest">
    <basecheck @click.native="outclick"></basecheck>
  </div>
</template>
<script>
// import basecheck from "./basecheck.vue";
import Vue from "vue";
Vue.component("basecheck", {
  template: `<button type="primary">innerButton</button>`
});

export default {
  name: "platform",
  components: {},
  methods: {
    outclick() {
      alert(`this is out`);
    }
  }
};
</script>
// 上面的方式 不加 .native 的话就不会执行
```



### 鼠标按钮修饰符

- `.left  /  .right  /  .middle`   左右中建点击

### 键值修饰符

- `.keyCode` 一些 `onkeyup  onkeydown`事件的时候可以指定某个键才触发

- ```
  //普通键
  .enter 
  .tab
  .delete //(捕获“删除”和“退格”键)
  .space
  .esc
  .up
  .down
  .left
  .right
  //系统修饰键
  .ctrl
  .alt
  .meta
  .shift
  ```

  > 可以通过全局 `config.keyCodes` 对象自定义按键修饰符别名

还可以将系统修饰符和其他键吗连接起来使用 `<input type="text" @keyup.ctrl.67="shot(4)"/>` 同时按下 `ctrl   c`的时候才触发

- `.exact` 有些场景我们只能按一个系统修饰键来触发，注意这个只是限制系统修饰键。

### v-bind 修饰符

- `.sync` 有时候我们需要对prop进行双向绑定，可以使用该修饰符，但是需要注意点

  - 使用 sync 的时候，子组件传递的事件名必须为`update:value` 其中value必须是与子组件中的props中声明的名称完全一致
  - 带有 `.sync`标识符的 `v-bind`不能和表达式一起使用，取而代之的是只能提供想要绑定的属性名，类似 `v-model`
  - 将`v-bind.sync`用在一个字面量的对象上，例如 `v-bind.sync=“{ title: doc.title }”`是无法正常工作的不能使用字面量的复杂表达式

  ```vue
  <comp :myMessage="bar" @update:myMessage="func"></comp>
  
  func(e){
    this.bar = e
  }
  // 子组件中
  func2(){
    this.$emit('update:myMessage', params)
  }
  
  
  //修饰符的写法 
  <comp :myMessage.sync="bar"></comp>
  
  this.$emit('update:myMessage', params)
  ```

  - `.prop` 学习该修饰符需要知道两个东西的区别

  ```
  Property: 节点对象在内存中存储的属性，可以访问和设置
  Attribute: 节点对象的其中一个属性 （property） 值是一个对象
  可以通过点访问法 `document.getElementById('xx').attributes`
  document.getElementById('xx').getAttributes('xx') 读取
  document.getElementById('xx').setAttribute('xx',value) 新增和修改。
  在标签里定义的所有属性包括 HTML 属性和自定义属性都会在 attribute 对象里以键值对的形式存在
  ```

  ```vu
  // 这里 id value style 都属于 property
  // index 属于 attribute
  // id title 等即是属性，也是特性，修改属性，其对应的特性会发生改变，修改特性，属性也会改变
  <input id="uid" title='title1' value='1' :index='index'/>
  // input.index === undefined
  // input.attributes.index === this.index
  ```

  从上面的代码可以看出我们如果直接使用`v-bind`绑定，会默认绑定到DOM节点的 attribute 所以为了  **通过自定义属性存储变量，避免暴露数据。**  **防止污染HTML结构**  所以可以使用修饰符

   ```vu
  <input id="uid" title="title1" value="1" :index.prop="index">
  // input.index === this.index
  // input.attributes.index === undefined
   ```

  

  - `.camel` 由于html不会区分大小写 `<svg :viewBox="viewBox"></svg>` 渲染的时候就变为全部小写了，不认识`viewbox`导致渲染失败， 使用该修饰符，它就会渲染为驼峰名， *使用字符串模版就没有这个限制*
  - native
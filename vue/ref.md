# vue的ref属性
ref 在 Vue 中是用来给元素或是子组件注册引用信息到父组件或是 Vue 实例上，注册后的引用信息都会呈现在 **父组件/Vue** 实例的 `$.refs` 上， 这时我们就可以通过 `$.refs` 获取到引用的 DOM 对象或是子组件信息,获得的是一个vue实例对象的形式

组件执行到 beforeMount 钩子函数时，Vue 虽然已经将模板编译完成，但是尚未挂载到页面 DOM 元素上，因此 ref 是在页面渲染完成后才被创建的。 可以在 mounted 周期函数内获取值，也可以获取子组件的值， 一个组件挂载的方式是会将该组件引用的所有子组件都加载完成再挂载到页面上


### 使用 ref 获取子组件对象
我们可以在子组件上面添加一个 ref 属性，然后在父组件就可以调用子组件的属性和方法，因为子组件是挂载在父组件实例的 refs 属性上的, 但是在实际开发的时候都不会这样使用， 闭合原则应该子组件只会操作内部的数据，可以暴露方法接口出来

```html
<template>
  <div class="about">
    <input type="text" ref="msgText" v-model="msg" />
    <button @click="getElement">获取元素</button>

    <hr>

    <child ref='childComponent'></child>
  </div>
</template>
<script>
import child from './child.vue'
export default{
  components:{child},
  data(){
    return{
      msg: 'Hello ref'
    }
  },
  mounted(){
    console.log(this)
    console.log('mounted: ' + this.$refs.msgText.value)
  },
  methods:{
    getElement(){
      console.log('input parent value is : ' + this.$refs.msgText.value)
      this.$refs.childComponent.getLocalData();   // 子组件的方法
      console.log('input children value is : ' + this.$refs.childComponent.local)
    }
  }
}
</script>
```
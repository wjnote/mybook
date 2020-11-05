# vue 插件系统
所有的 Vue 插件都会暴露一个 `install` 方法，当执行 `Vue.use` 时，实质上 `Vue` 会执行插件的 `install` 方法， 方法中第一个参数是vue实例对象.


插件系统的方法和属性一般是给 `Vue.prototype` 添加到原型链上， 然后在` main.js` 引入并执行，这样

```js
// plugins.js
export default {
  install(Vue, options){
    Vue.prototype.$myGlobalMethod = opts => {
      window.alert(JSON.stringify(opts))
    }
  }
}
```


页面加载的时候就会执行插件的方法, 这样就可以设置原型链的变量和方法，然后在 `.vue` 文件中直接访问vue实例就可以访问到，
```js
// main.js
import Vue from 'vue'
import MyPlugins from 'plugins.js'
// 这样就把插件中的方法都挂载到vue实例的原型链上了，所有的子组件都可以直接访问原型链上的方法和属性
Vue.use(MyPlugins);
```

```js
export default {
  name: 'app',
  mounted() {
    this.$myGlobalMethod({ code: 0, status: 'success', msg: '成功' })
  },
}
```

使用`Vue.use()`引用外部插件的话，则该方法默认会调用插件的`install`方法。引入的组件类型必须为`Function`或者`Object`。  如果是个对象，必须提供`install`方法. 如果是一个函数，会被直接当作install函数执行 
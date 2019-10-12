# vue 插件系统
所有的 Vue 插件都会暴露一个 install 方法，当执行 Vue.use 时，实质上 Vue 会执行插件的 install 方法， 在install 方法中第一个参数是vue实例



# 获取vue全局变量

关于如何在`.vue` 文件中访问Vue全局变量，全局js，一般是给`Vue.prototype` 添加实例方法，在 main.js 引入，全局配置路由导入

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

```js
// main.js
import Vue from 'vue'
import MyPlugins from 'plugins.js'

Vue.use(MyPlugins)
```

```js
export default {
  name: 'app',
  mounted() {
    this.$myGlobalMethod({ code: 0, status: 'success', msg: '成功' })
  },
}
```
页面加载的时候就会执行该方法,这样就可以设置全局的变量和方法
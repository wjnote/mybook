# vuex记录
### vuex中的store就是一个单例模式
一个vue实例只能对应一个`store`实例，如果不在根组件注入，所有组件都可以引入，此时单例模式的作用就体现了，不管注册多少次，返回的都是第一次所创建的那唯一的一个实例
```js
// 单例模式 代码实现
class Singleton {
  // constructor 方法返回的对象就是实例对象 默认返回 this
  constructor(){
    if(!Singleton.instance){
      Singleton.instance = this;
    }
    return Singleton.instance;
  }
  toString(){
    return '[object Singletion]'
  }
}
```

vuex 插件是一个对象，他在内部实现了一个 install 方法，这个方法会在插件安装的时候被调用，从而把sotre注入到vue实例中，每install一次，都会尝试给vue实例注入一个Store，如果源码中没有实现单例模式，每次都是注入一个新的Store，这之前所有的操作都被清空了

```js
let Vue  

export function install(_Vue){
  if(Vue && _Vue === Vue){
    if(process.env.NODE_ENV !== 'production'){
      console.error('[vuex] aleardy installed')
    }
    return;
  }
  // 若没有 则为这个Vue实例对象install一个唯一的vuex
  Vue = _Vue;
  applyMixin(Vue);  // 将Vuex的初始化逻辑写进Vue的钩子函数里
}
```





#### 基本概念
State:   this.$store.state.XXXX  /  mapState   取值

Getter:  this.$store.gettter.XXXX   /  mapGetters 取值

Mutation:   this.$store.commit('XXXX')  / mapMutations   赋值

Action: this.$store.dispatch('xxxx')    / mapActions   赋值

Module


#### 底层原理，核心原理
State:  提供一个响应式数据

Getter:  借助 Vue 的计算属性 computed 来实现缓存

Mutation:   更改state方法

Action:  触发 mutation 方法

Module:  Vue.set 动态添加到state到响应式数据中


##### vuex的核心代码 将 state挂载到vue实例的上面，实现双向绑定
```js
import vue from 'vue'

const Store = function Store(options = {}){
    const {state = {}, mutations={}} = options;
    //  vuex 实现响应式的原理 是作将数据挂载到一个vue实例上面
    this._vm = new Vue({
        data:{
            $$state: state
        }
    })
    this._mutations = mutations;
}
Store.prototype.commit = function(type, payload){
    if(this._mutations[type]){
        this._mutations[type](this.state, payload)
    }
}
// 将数据代理一下，如果访问store的state属性，其实就是访问的上面的vue实例的数据，
Object.defineProperties(Store.prototype, {
    state:{
        get: function(){
            return this._vm._data.$$state
        }
    }
})
export default {Store}
```
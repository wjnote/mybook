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

`vuex` 插件是一个对象，他在内部实现了一个 `install` 方法，这个方法会在插件安装的时候被调用，从而把`sotre注入到vue`实例中，每install一次，都会尝试给vue实例注入一个Store，如果源码中没有实现单例模式，每次都是注入一个新的Store，这之前所有的操作都被清空了

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


### vuex 辅助函数
`mapState`, `mapGetters`, `mapActions` 和 `mapMutations` 这些函数的作用和在绑定命名空间的的模块时区别
> state 属性辅助函数

```js
  computed:{
    // 当映射的计算属性名称和state的子节点的名称一致时，可以穿字符串
    ...mapState(['count', 'token']),
    ...mapState({
      // 箭头函数更加简练
      token: state=> state.token,
      // 传递 count 字符串等同于  state=> state.count
      'number':'count',
      // 为了获取到 this 作为局部状态，必须使用常规函数
      countPlusLocal:function(state){
        return state.count + this.localNum;
      }
    }),
  }
```

> Getters 辅助函数 mapGetters

```js
 computed: {
  //  使用对象扩展符将 getter 混入到 computed 中，
   ...mapGetters(['doneTodo', 'another']),
  // 对象的方式  将 this.doenTodoCount 映射为 this.$store.getters.doneTodo
   ...mapGetters({
      doneTodoCount: 'doneTodo'
   })
 }
```

> mapMutations 的辅助函数

```js
  // 调用 mutation的两种方式  都可以传入参数
  store.commit('mutationName', paramsObj);  // 以参数形式触发
  store.commit({   // 以对象形式分发
    type: 'mutationName',
    otherParams: otherParams,
  });

  methods:{
    // 传入数组的形式
    ...mapMutations([
      // 将 this.increment() 映射为 this.$store.commit('increment')
      'increment',  
      // mapMutations 也支持参数
      'incrementBy', // 将 this.incrementBy(params) 映射为 this.$store.commit('incrementBy', params)
    ]),
    // 传入对象的形式，就类似于设置别名了
    ...mapMutations({
      // 将this.add() 映射为 this.$store.commit('increment')
      add: 'increment'
    })
  }
```

> mapActions

```js
  // 触发的方式两种形式
  store.dispatch('increment', paramsObj);
  store.dispatch({type: 'increment', params: paramsObj});

  methods:{
    ...mapActions([
      'increment',  // 将 this.increment()  映射为 this.$store.dispatch('increment')
      // mapMutations 也支持参数
      'incrementBy', // 将 this.incrementBy(params) 映射为 this.$store.commit('incrementBy', params)
    ]),
    ...mapActions({
      // 将this.add() 映射为 this.$store.dispatch('increment')
      add: 'increment'
    })
  }
```

### vuex的核心代码 将 state 挂载到vue实例的上面，实现双向绑定
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


### vuex 持久化数据
在开发中会发现如果刷新页面，就会导致js重新加载，vuex的Store是保存在JS内存中的，数据就重置了，为了防止这种情况，我们一般会将数据也存储在 localStorage 中，每改动一次手动存储一次的话比较麻烦，就可以使用vuex提供的插件功能。

```js
// vuex插件写法
const myPlugin = store = >{
  // 当初始化Store 之后调用
  store.subscribe((mutation, state)=>{
    // 每次mutation修改数据后调用   mutation 格式为 {type: payload }
    localStorage.setItem('store', JSON.stringify(state))
  })
}
// 在vuex中都是通过mutation来修改数据的，同步的修改数据


// 使用方式
const store = new Vuex.Store({
  // ...
  plugins: [myPlugin]
})
```

### vuex源码解析
在 `src/index.js` 文件最下面可以看到vuex暴露出去的接口，    在`helpers.js` 就有 mapState的实现
```js
  export default{
    Store, install, mapState, mapMutations, mapGetters, mapActions
  }
  // 定位到最前面可以看到引入了,, 
  import { mapState, mapMutations, mapGetters, mapActions } from './helpers'
```

想知道`mapState`这个方法的实现，就需要先知道 `normalizeMap`的实现, **该方法主要是格式化mapState传进来的参数，state传进来的参数只能是数组或者是对象的方式传入**
```js
 function normalizeMap(map){
   return Array.isArray(map) ? map.map(key => ({key: key, val: key})) 
   : Object.keys(map).map(key=>({key: key, val: key}))
 }
```
```js
  // state传入的参数
  mapState(['count', 'add'])
  mapState({count: state=> state.count, countAlias:  'count'})

  // 经过 normalizeMap 处理之后的数据格式    格式就变为一样的形式，数组中是对象的方式
  [{key: 'count', val: 'count'}, {key: 'add', val: 'add'}]

  [{key: 'count', val: state=>state.count}, {key: 'countAlias', countAlias: 'count'}]
```

上面经过`normalizeMap`的格式化之后，在传入 `mapState`方法中,返回一个符合 `computed`的对象，`normalizeMap`数组中对象的值有2中情况，如果不是函数就直接查找，如果是函数，则需要使用 `call` 将`val`这个函数的this指向vue实例，然后传入 state 和`getters`，最后执行val函数。
```js
  export function mapState(states){
    const res = {};
    normalizeMap(states).forEach(({key, val})=>{
      res[key] = function mappedState(){
        return typeof val === 'function'
        ? val.call(this, this.$store.state, this.$store.getters)
        : this.$store.state[val]
      }
    })
    return res;
  }
  // mapState 代码不多，主要功能就是将传入的数组或者对象转换成computed计算属性能识别的代码
```

如果val是函数，可能有点难理解，例如传入的参数可能是下面的形式
```js
  computed: mapState({
    countPlus(state){
      return state.count + this.localCount
    }
  });

  // 1. 经过 normalizeMap 处理后的对象是
  [
    {key: 'countPlus',
    val: function(state){
      return state.count + this.localCount
    }
    }
  ]
  // 2. 再经过mapState 最后返回的是res 这里会将val函数执行一遍，将函数的返回值return出来
  {
    countPlus: function mappedState(){
      return this.$store.state.count + this.localCount
    }
  }
```


> 这个只是一个基本实现原理，网上有比较完善的npm包可以直接使用  例如  `vuex-persistedstate`
# vuex记录

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


##### vuex的核心代码
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
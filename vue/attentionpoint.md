# vue需要注意的问题
记录一些vue在使用中需要注意点和遇到的坑

### 1 组件里面，`data` 必须是一个函数
应用类型的数据结构，会导致vue组价实例修改了一处，其他地方也会修改，所以必须要data函数的形式，每次都创建一个新实例，返回一个全新的副本


### 2 vue中 $set 的使用场景
> vue在创建实例的时候把data深度遍历所有属性，并使用 `Object.defineProperty` 把属性全部转变为 `getter/setter`,让vue追踪，所以如果开始不确定有哪些属性，可以使用`Object.assign`的方式添加响应数据
1. 如果我们想用数组的下标去修改数组的值，数据是可以修改的，但是不会触发响应式更新，也不会触发 `update` 函数， 这个时候就可以使用 $set的方式
2. vue中不能检测到对象属性的添加和删除，有时在初始化的时候不确定对象有哪些属性，需要后续添加的情况，直接给对象的属性赋值，会修改数据，但是不会触发视图更新，也不会触发 `update` 函数

```js
export default{
  data(){
    return {
      items: ['a','b','c'],
      info: {},
    }
  },
  update(){
    console.log('数据更新后的数据...',this.items)
  },
  methods:{
    changeItem1 () {
      // 这种情况是可以改变数据，但是不会触发视图更新 也不会触发update函数
        this.items[0] = 'x';
        console.log(111, this.items[0]);
    },
    changeItem2 () {
        this.$set(this.items, 0, 'x');

        this.$set(this.info, 'age', '12');
        this.info = Object.assign({}, this.info, {age: '12', work: 'code'});
    }
  }
}
```

### 3  生命周期函数
需要注意生命周期函数内数据是否已经初始化和DOM节点是否已经挂载。然后其中有`activated`和`deactivated`特殊的生命周期函数与`<keep-alive></keep-alive>`这个组件有关

### 4  生命周期函数/methods/watch 里面不要使用箭头函数
生命周期函数/methods/watch被混入到vue实例中，可以直接通过VM实例访问到这些方法，或者在指令中使用，方法中的this绑定为vue实例

箭头函数和普通函数最大的区别就是this的指向问题。 箭头函数this指向函数所在的域，普通函数this指向当前调用栈的执行上下文环境

在vue中生命周期函数，methods , watch 自动绑定了 this 为 vue实例，所以可以在函数中访问到数据，如果用箭头函数，箭头函数会自动绑定执行上下文，因此this与你期待的vue实例不同，但是还是可以使用参数的形式，第一个参数就是当前vue实例

### 5 methods / computed / watch
1. `methods / computed ` :  我们可以将同一个函数定义为methods或者computed，行为是一样的，不同的是computed 是基于他们的依赖进行缓存的，计算属性只有在他依赖发生改变时才重新求值
2. `computed /  watch `  watch有新旧两个参数，计算属性没有，但是计算属性可以从setter取值新值
3.` computed `： vue的计算属性 computed 默认只有getter，需要使用getter的时候要自己加一个setter

```js
  export default{
    computed:{
      fullName(){
        return this.firstName + ' ' + this.lastName
      }
    },
    // 或者使用下面的方式来写
    computed:{
      fullName:{
        get: function () {
          return this.firstName + ' ' + this.lastName
        },
        set: function(newvalue){
          let names = newvalue.split(' ');
          this.firstName = names[0]
          this.lastName = names[names.length - 1]
        }
      }
    }
  }
```

### 6 循环遍历
当我们需要在循环遍历的时候做一个逻辑是否的判断，最好的方式是使用计算属性来计算，这样可以将计算和页面展示分开，不会耦合在一起,直接在html中使用 `showList` 而不是`showItems`

```js
export default{
  computed:{
    showList:function(){
      this.showItems.filter(item=>item.isShow == true)
    }
  }
}
```

### 7 组件复用意味着组件的生命周期钩子函数不会被调用
`router-view`是复用的， 新闻列表的时候，只改变id值的情况，就不会刷新，不会执行组件的生命周期函数 `created beforeMounted mounted`

1. 使用watch方法，也是官方推荐的方式，当id值改变时，`$route`也会相应发生改变，可以通过watch的方法来操作
    ```js
      watch: {
        '$route':function(to, from){
          // dosomethings
          // 将需要执行的内容写为 一个函数
        }
      }
    ```
2. 通过改变`<router-view>`中的key来达到刷新的目的，
    ```js
      <router-view :key="activeDate"></router-view>
      // 我用了一个简单粗暴的方式来改变key，时间戳
      // this.activeDate = new Date()
    ```
3. 官方文档，如果使用了`vue-router`, 则在组件内直接使用`beforeRouteLeave`
    ```js
      beforeRouteLeave(to, from, next){
        // 导航离开该组件的对应路由时调用
        // 可以访问组件实例 this
      }
    ```

### 8 在组件中引用其他组件的时候，引用组件的变量名不能和该组件的名称重复，否则会爆栈


### 9 Vue里面router-link在电脑上有用，在安卓上没反应怎么解决？
Vue路由在Android机上有问题，babel问题，安装babel polypill插件解决。

### 10 Vue2中注册在router-link上事件无效解决方法
 使用@click.native。原因：router-link会阻止click事件，.native指直接监听一个原生事件。

### 11 RouterLink在IE和Firefox中不起作用（路由不跳转）的问题
方法一：只用a标签，不适用button标签；方法二：使用button标签和Router.navigate方法


### vue开发中的坑于感悟
1. 使用keep-alive包裹的组件/路由，打开一次后created只会执行一次，有两种情况，一、如果要重新渲染部分数据，可以在activated中做处理；二、路由/组件重新重新created，可以使用官方推荐的:key="key" ，然后去改变key的值，组件就会重新挂载了
2. `beforeRouteEnter`中的next函数的执行时间是在组件mounted之后，因此需要在此处处理的数据要注意了
3. 网页刷新时`vuex`数据会丢失，需配合`localStorage`或`sessionStorage`使用，数据先存后取
4. 对于权限及不确定路由，可以使用`addRoutes()`，可以避免抖动
5. 使用`computed`替代`watch`，`computed`依赖于data属性的更改，是有缓存的
6. 通过props传递的值，不要在子组件去更改。开发中，如果直接更改props，一、基本类型的值会报错，二、引用类型的值不会报错，但是不好去追溯数据的更改，很多人不太注意引用类型，可通过computed或watch去更改

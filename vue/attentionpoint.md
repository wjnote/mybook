# vue需要注意的问题
记录一些vue开发中需要注意点的点

###  1  组件里面，data必须是一个函数
应用类型的数据结构，会导致vue组价实例修改了一处，其他地方也会修改，所以必须要data函数的形式，每次都创建一个新实例，返回一个全新的副本


### 2  vue中 $set 的使用场景
1. 如果我们想用数组的下标去修改数组的值，数据是可以修改的，但是不会触发响应式更新，也不会触发 `update`函数， 这个时候就可以使用 $set的方式
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
        console.log(222, this.items[0]);
    },
    addProperty () {
      // 这种情况是可以改变数据，但是不会触发视图更新 也不会触发update函数
      this.info.age = '12';
      console.log(555, this.info);
    },
    addProperty2(){
      this.$set(this.info, 'age', '12');
      this.info = Object.assign({}, this.info, {age: '12', work: 'code'});
      // 使用对象的形式，可以一次性添加多个属性
    }
  }
}
```

> vue在创建实例的时候把data深度遍历所有属性，并使用 `Object.defineProperty` 把属性全部转变为 `getter/setter`,让vue追踪，所以如果开始不确定有哪些属性，可以使用`Object.assign`的方式添加响应数据


### 3  生命周期函数
需要注意生命周期函数内数据是否已经初始化和DOM节点是否已经挂载。然后其中有`activated`和`deactivated`这两个函数与`<keep-alive></keep-alive>`这个组件有关

### 4  生命周期函数/methods/watch 里面不要使用箭头函数
生命周期函数/methods/watch被混入到vue实例中，可以直接通过VM实例访问到这些方法，或者在指令中使用，方法中的this绑定为vue实例

箭头函数和普通函数最大的区别就是this的指向问题。 箭头函数this指向函数所在的域，普通函数this指向当前调用栈的执行上下文环境

在vue中生命周期函数，methods , watch 自动绑定了 this 为 vue实例，所以可以在函数中访问到数据，如果用箭头函数，箭头函数会自动绑定执行上下文，因此this与你期待的vue实例不同，但是还是可以使用参数的形式，第一个参数就是当前vue实例

### methods / computed / watch

1. methods / computed  :  我们可以将同一个函数定义为methods或者computed，行为是一样的，不同的是computed 是基于他们的依赖进行缓存的，计算属性只有在他依赖发生改变时才重新求值
2. computed /  watch   watch有新旧两个参数，计算属性没有，但是计算属性可以从setter取值新值
3. computed ： vue的计算属性 computed 默认只有getter，需要使用getter的时候要自己加一个setter

    ```js
      export default{
        computed:{
          fullName(){
            return this.firstName + ' ' + this.lastName
          }
        }
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


### 循环遍历
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



### 5  组件复用意味着组件的生命周期钩子函数不会被调用
router-view是复用的 有时候会有新闻列表的时候，只改变id值的情况，这是就不会刷新，不会执行组件的生命周期函数

1. 使用watch方法，也是官方推荐的方式，当id值改变时，$route也会相应发生改变，可以通过watch的方法来操作
    ```js
      watch: {
        '$route':function(to, from){
          // dosomethings
          // 将需要执行的内容写为 一个函数
        }
      }
    ```
2. 通过改变 <router-view>中的key来达到刷新的目的，
    ```js
      <router-view :key="activeDate"></router-view>
      //我用了一个简单粗暴的方式来改变key，时间戳（捂脸）
      //this.activeDate = new Date()
    ```
3. 官方文档，如果使用了 vue-router ,则在组件内直接使用`beforeRouteLeave`
    ```js
      beforeRouteLeave(to, from, next){
        // 导航离开该组件的对应路由时调用
        // 可以访问组件实例 this
      }
    ```

### 6   在组件中引用其他组件的时候，引用组件的变量名不能和该组件的变量重复，否则会爆栈

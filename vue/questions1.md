# 常见问题列表-1

#### Q: vue-router 的动态路由，只修改 `/detail/:id` 后面的id值，页面会缓存不会刷新，也不会执行生命周期函数
    A: 解决办法： 主要是针对路由的监听，或者设置组件不缓存
```html
    //  方式一
    watch:{
        '$route'(to, from){
            // 调用方法
        }
    }

    // 方式二
    <router-view :key="key"></router-view>
    computed:{
        key(){
            return this.$route.name !== undefined?
            this.$route.name + +new Date():
            this.$route + +new Date()
        }
    }
```

    1. 方式一中最好将初始化的内容写入一个方法中，id改变的时候不会再执行生命周期函数
    2. 方式二中将直接就是不需要缓存的,用于嵌套了多个子组件的，但是会导致HTTP请求增多


#### Q:  keep-alive 通常我们需要缓存一些组件，但是有时候我们也需要通过一些状态或者参数来刷新页面，这时我们就需要更新缓存
    A: **解决办法：** 在路由信息中配置 `keep-alive` 为true时，将会缓存当前页面，第一次进入一个缓存的页面时，vue的生命周期执行的顺序为`created() -> mounted() -> activated()`, 这里的`activated()`钩子只存在于keep-alive为true的路由页面，之后每次进入这个缓存页面的时候，都不会再去执行vue的生命周期，而是从缓存里面拿，只有`activated()`钩子在每次进入这个缓存页面是才会执行，当然还有一个钩子`deactivated()`是在离开页面的时候会执行.
    
    这里就存在一个坑，当我们需要通过子组件或者其他的页面传递参数，来更新这个缓存页面的DOM时，发现按正常逻辑处理数据获取的部分一直不会调用，这时就要在`activated()`里面也放一分请求数据的逻辑来更新dom, 这只是其中的一种解决办法。
    
    **第二种办法是：**通过`beforeRouteEnter()和beforeRouteLeave()`这两个钩子来解决，注意这两个钩子是在vue2.2之后才能使用。例如在搜索页输入关键字，跳转到列表页进行数据获取，而列表页是`keep-alive`为 true，那么此时我们只需要在搜索页的生命周期中配置下面的代码就ok了， to 表示即将要进入的页面的路由对象，通过更改keepAlive属性，以达到更新列表页的目的。还有一种情况，就是从列表页到详情页的时候，往往都需要缓存列表的筛选条件或者结果，那么在列表页的`beforeRouteLeave()`中配置`from.meta.keepAlive=true`就可以了，那么再次从详情页返回列表的时候，列表筛选结果的状态还存在 这种体验，就相当到位了。
    ```js
    beforeRouteLeave(to,from,next){
        to.meta.keepAlive = false;
        next();  // next 方法是必须调用的
    }
    ```


#### Q: echarts 图表不显示，在vue中使用UI框架tab切换时，如果隐藏的那个块有`echarts`图的内容，切换之后显示的DOM就不会显示图表，先显示的DOM中 `echarts` 是可以显示的
    A： **因为echarts只能在显示的容器中渲染,tab页面未展示的页面没有渲染，所以DOM没有宽高** 解决办法：在容器切换之后再渲染图表，也就是在触发切换的事件中操作echarts初始化
    有时候图表会放在多个标签页里，那些初始隐藏的标签在初始化图表的时候因为获取不到容器的实际高宽，可能会绘制失败，因此在切换到该标签页时需要手动调用 `resize` 方法获取正确的高宽并且刷新画布，或者在 opts 中显示指定图表高宽。

```js
drawCharts(){
  // 基于准备好的dom，初始化echarts实例,dom容器中需要在调用echart.init之前，
  // 必须给图形dom加上对应的宽度和高度，不然echarts不能渲染
  document.getElementById("container").style.display = "block";
  let myChart = this.$echarts.init(document.getElementById('container'));
  // 绘制图表
  receiveChart.setOption({});
}

// 或者可以考虑下面的方式，在切换的时候
element.on('tab(twoTabs)', function(elem){
  //切换tab页，获取正确的宽高并刷新画布
  myChart.resize();
});
```


#### Q: vuex 中要注意，如果state是数组的话，在外部直接应用了，然后直接改变了，就会报错
    A: 在`commit`之外修改state的值因为是引用数组，所以在使用的时候需要 `state.array.slice()` 这样相当于复制了一个副本来操作，如果state是一个对象的话，可以使用 `Object.assgin()` 或者是 `JSON.parse(JSON.stringify(stateObj))` 的方式来拷贝一份


#### Q: vue子组件在`router-view`中渲染不出来
    A: 可能是父组件和子组件有`name`属性，把`name`属性去掉就可以了，这好像是设计的bug


#### Q: `<block></block>` 元素的使用
    A: vue文件中如果不想使用太多的DOM节点，可以使用 `<block></block>` 这只是一个包裹元素，在最后HTML节点中是不会被渲染出来的，可以使用在列表循环渲染


#### Q:  Vue中的key的作用
    A: key是给每一个 vnode 的唯一ID，依靠key，我们的diff算法可以更准确，更快捷。diff 算法的过程中会先进行新旧节点的首尾交叉对比，当无法匹配的时候，会用新节点的key与旧节点进行对比，从而找到旧节点。 key的唯一性可以被 Map 数据结构充分利用，遍历查找的时间复杂度很小

#### Q: 在 `beforeUpdate  updated` 中万万不可修改更改依赖数据
    A： 在 `beforeUpdate` 中主要是移除已经添加的事件监听器等，在 updated 中主要是是操作DOM添加事件监听等，在这两个生命周期函数中改了依赖数据就会陷入死循环

#### Q: 在组件中如果想在元素上绑定 `data-`类型额属性，然后在方法中获取的对应的属性
​	   A : 在元素上绑定事件，然后在事件中获取 `e.target.dataset` 就可以获取到所有绑定的`data-` 属性对象
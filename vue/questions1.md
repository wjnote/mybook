# 常见问题列表-1

#### 1. vue-router 的动态路由，只修改 `/detail/:id` 后面的id值，页面会缓存不会刷新，也不会执行生命周期函数

解决办法： 主要是针对路由的监听，
```vue
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


#### 2. keep-alive
keep-alive导致的问题，通常我们需要缓存一些静态页面，但是有时候我们也需要通过一些状态或者参数来刷新页面，这时我们就需要更新缓存

**解决办法：** 在路由信息中配置 keep-alive 为true时，将会缓存当前页面，第一次进入一个缓存的页面时，vue的生命周期执行的顺序为created() -> mounted() -> activated(),这里的activated()钩子只存在于keep-alive为true的路由页面，之后每次进入这个缓存页面的时候，都不会再去执行vue的生命周期，而是从缓存里面拿，只有activated()钩子在每次进入这个缓存页面是才会执行，当然还有一个钩子deactivated()是在离开页面的时候会执行.

这里就存在一个坑，当我们需要通过子组件或者其他的页面传递的 参数，来更新这个缓存页面的DOM时，发现按正常逻辑处理数据获取的部分一直不会调用，这时就要在activated()里面也放一分请求数据的逻辑,来更新dom.这只是其中的一种解决办法。

**第二种办法是：**通过beforeRouteEnter()和beforeRouteLeave()这两个钩子来解决，注意这两个钩子是在vue2.2之后才能使用。例如在搜索页输入关键字，跳转到列表页进行数据获取，而列表页是keep-alive为true的，那么此时我们只需要在搜索页的生命周期中配置
```js
beforeRouteLeave(to,from,next){
    to.meta.keepAlive = false;
    next();  // next 方法是必须调用的
}
```

就ok了，to表示即将要进入的页面的路由对象，通过更改keepAlive属性，以达到更新列表页的目的。还有一种情况，就是从列表页到详情页的时候，往往都需要缓存列表的筛选条件或者结果，那么在列表页的`beforeRouteLeave()`中配置`from.meta.keepAlive=true`就可以了，那么再次从详情页返回列表的时候，列表筛选结果的状态还存在 这种体验，就相当到位了。
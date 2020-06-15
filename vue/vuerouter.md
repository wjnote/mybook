# vue-router
可以配置动态路由 `/user/:id` ，然后在组件中获取其中的参数，如果id值可能有也可能没有就可以使用 `/user/:id?` 的形式，当URL `/user` 的时候也会匹配

> 如果我们需要设置ID必须为数字类型的，可以使用正则表达式 ,以圆括号的形式加入 '/user/:id(\\d+)'  这样我们传递数字以外的内容，组件就无法接收到


### router.addRoutes
```js
// 主要的是下面第一行代码，如果没有这行代码，router.options.routes 不是响应式的。
this.$router.options.routes = routes;
this.$router.addRoutes(routes);
```

### 路由传参
通过URL来传参是一个基本需求，传参的方式有几种类型

1. 在路由文件中通过配置 name 来传参，然后通过 `$route.name` 来接受参数，这种方式很少用
    ```vue
      routes:[{ path: '/', name: 'Hello', component: Hello}];
      <p>{{ $route.name }}</p> 
    ```
2. 可以通过 `<router-link>` 标签中的 `to` 来传参 


### 重定向方式
使用 `redirect` 的方式来重定向路由，一般使用在最后的位置设置 404页面到指定的页面。

如果重定向后需要传递参数的话，可以直接像` router `的参数一样传递，这样在重定向后的组件也能获取到传递的参数

```js
  {
    path: '/user/:id(\\d+)',
    component: User
  },{
    path: '/newUser/:id(\\d+)',
    redirect: '/user/:id(\\d+)'
  }
```

还可以使用别名的方式来模拟重定向效果，和 `redirect` 的不同就是URL不会改变

> 有个坑需要注意就是 别名的配置不能使用在 path: '/'  中，否则，别名是没效果的
```js
  {
     path: '/content',
     component: Hello,
     alias: '/home'  
  }
  {  // 这个配置是不会有效果的
    path: '/',
    component: Home,
    alias: '/home'
  }
```
# vue-router
可以配置动态路由 `/user/:id` ，然后在组件中获取其中的参数，如果id值可能有也可能没有就可以使用 `/user/:id?` 的形式，当URL `/user` 的时候也会匹配



### router.addRoutes
```js
// 主要的是下面第一行代码，如果没有这行代码，router.options.routes 不是响应式的。
this.$router.options.routes = routes;
this.$router.addRoutes(routes);
```
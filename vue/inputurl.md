# vue地址栏输入URL无效

项目有一个需求，在地址栏直接输入路由，跳转到对应的页面组件，在开发环境中是可以这样跳转的，但是项目打包之后，通过地址栏跳转就会报错。

因为vue在页面上显示那个组件是根据`vue-router`来控制的,在地址栏上直接输入路由名称，并不能触发`vue-router`的规则，所以只能通过监听地址的改变，利用回调函数在组件内部进行动态修改路由


#### 方式一  history 模式
vue-router 默认的是hash模式，通过更改为 `history`模式可以解决这个问题，但是需要后端的配合，更改服务端配置，



#### 方式二  hashchange 事件
更改hash不会导致整个页面重新加载，而且可以定位到元素的id或name与之相同的位置(锚点)

`window.location.hash` 可以获取URL中的hash值，通过监听 hash 的状态，来动态修改vue-router的路由

```js
window.addEventListener('hashchange', function(e){
  console.log(e.oldURL)
  console.log(e.newURL)
}, false)
```

```js
mounted(){
  window.addEventListener('hashchange',()=>{
		var currentPath = window.location.hash.slice(1); // 获取输入的路由
		if(this.$router.path !== currentPath){
			this.$router.push(currentPath); // 动态跳转
		}
	},false);
}
```
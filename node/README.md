# node

之所以说 Node.js 是单线程，就是因为在接受任务的时候是单线程的，它无需进程/线程切换上下文的成本，非常高效，但它在执行具体任务的时候是多线程的。因为CPU是多核的，

### node中的全局变量

在node中有全局变量 `process` 表示当前的node进程，`process.env`包含了关于系统环境的信息。

```shell
C:\Users\Administrator> node
> process.env
{ ALLUSERSPROFILE: 'C:\\ProgramData',
  APPDATA: 'C:\\Users\\Administrator\\AppData\\Roaming',
  AWE_DIR:
   'C:\\Program Files (x86)\\Khrona LLC\\Awesomium SDK\\1.6.6\\'
   //...............
}
```





用node来启动项目的前端项目的时候，有时候会提示 JavaScript堆内存不足，`Ineffective mark-compacts near heap limit Allocation failed - JavaScript heap out of memory`  在node中通过 JavaScript使用内存时只能使用部分的内存(64位系统下约为1.4G，32位系统下约为1.7G)，如果`webpack`项目比较大的时候就会超过V8对node默认的内存限制大小，解决办法可以使用 

```js
node --max-old-space-size=4096 test.js   // 单位为MB
node --max-new-space-size=4096 test.js   // 单位为KB

//vue项目启动的话 
'build' : 'node --max_old_space_size=4096 build/build.js'
```

第二种方式是使用一个 `npm`包 `increase-memory-limit`

```js
npm install -g increase-memory-limit  // 全局安装包
increase-memory-limit  // 在当前项目执行
```



### node 语言的十大失误

1. node没用JavaScript异步处理Promise对象
2. 低估了安全的重要性
3. 使用了 gyp 来实际 Build 系统
4. 没有使用 FFI  而继续使用 gyp
5. 过度依赖npm (内建 package.json支持)
6. 太容易造成require (任意模块)
7. `package.json` 建立了错误的模块概念
8. 臃肿复杂的 node_module 设计和下载黑洞
9. require(‘module’)  时没有强制加上 `.js` 扩展名
10. 无用的 `index.js` 设计


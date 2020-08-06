# node进程守护

node运行项目的时候，如果关闭了界面，服务也会停止，所以网上有一些包可以让node进程在后台一直运行。主要是3个常用的插件

- nodemon:  开发环境使用，修改后自动重启
- forever:  管理多个站点，每个站点访问量不大，不需要监控
- pm2:  网站访问量比较大，需要完整的监控界面


**主要学习 pm2 在Linux上面也是使用的 pm2**

### pm2 常用的命令

1. 启动单个文件
```shell
pm2 start filename.js      # 启动一个单独的文件
pm2 start filename.js -i 4  # 启动4个应用实例 自动负载均衡

# 本地开发，可以配合 pm2 logs 来使用，
pm2 start filename.js -- watch  # 监听文件变化
```
> pm2 启动的服务都是在后台运行，如需部署 docker上，需要加上 `--no-deamon`, 
> 而node默认前台运行， 可通过下面的方式原生支持

```shell
pm2 start filename.js --no-deamon
# or
nohup node filename.js &
```

2. 启动多个文件，批量启动
```js
// 新建一个 server.json 文件
{
  "apps": [{
    "name": "appA",
    "script": "./appA.js",
    "watch": true
  },{
    "name": "appB",
    "script": "./appB.js",
    "watch": false
  }]
}

`pm2 start server.json`
```
批量启动是以restart模式启动，可以多次执行

3. 重启
```shell
pm2 restart app_name|app_id   # 重启 某个进程或者进程ID
pm2 restart all  # 重启所有进程，相当stop+start
pm2 reload all  # 0秒停机重载进程 (用于不间断进程)
```

4. 查看
```shell
pm2 list  # 查看进程
pm2 logs  # 查看日志
pm2 show app_name|app_id  #  查看进程详情
pm2 monit  # 查看CPU和内存资源的占用
```

5. 停止
```shell
pm2 stop app_name|app_id
pm2 stop all  # 停止所有
```

6. 删除
```shell
pm2 delete app_name|app_id  # 从列表中删除指定的进程
pm2 delete all  # 从列表中删除全部进程
pm2 kill  # 杀死守护进程
```

7. 开机自启动
```shell
pm2 startup  # 创建开机自启命令
pm2 save  # 保存当前应用列表
pm2 resurrect  # 重新加载保存的应用列表
pm2 unstartup  # 移除开机自启
```

8. pm2 更新
```shell
pm2 save  # 保存当前应用列表
npm install mp2 -g
pm2 update
```

### pm2的主要特性
- 内建负载均衡(使用 node cluster 集群模块)
- 后台运行
- 0 秒停机重载
- 开机自启动脚本
- 停止不稳定的进程（避免无限循环）
- 控制台检测
- 提供远程控制和实时的接口API（允许和PM2 进程管理器交互）
# mybook
学习笔记电子书，是用 Gitbook 的来整理，使以前学到的知识点不再零散，任何一个小知识点，深挖下去，也是非常有意思的。

### gitbook 使用流程
```shell
安装
yarn global add gitbook-cli

在目录中：  创建README.md / SUMMARY.md 两个文件
gitbook init

页面有book.json 里面一般会有plugins插件,直接下载所有插件和主题
gitbook install

编辑的时候查看
gitbook serve [--prot xxxx]

最后打包的
gitbook build
```

### 快速生成目录结构，可以使用命令行的方式
1. 直接在 SUMMARY.md 文件中建立好文件的目录
2. 在命令行中执行 `gitbook init` 会根据目录自动生成相应文件
3. 但是不会覆盖你自己建立的文件目录结构


### readme.md 中树状图的生成
这不是markdown的语法，而是通过tree命令生成的，Linux下的tree命令很强大，支持很多参数，但是在window上支持的很少
> macOS 默认不支持tree命令，可以通过homebrew安装，`brew install tree -g`

- /F 显示每个文件夹中文件的名称。（带扩展名） **工作中最常用命令**
- /A 使用 ASCII 字符，而不使用扩展字符。
- `>tree.txt` 可以将树状图的结构输出为当前目录下的 txt 文件

> 在 .md 文件中树状图可以放在 ```html 格式中，放在js格式中会报错


### gitbook 插件系统
GitBook 插件本质上和 Node.js 包一样，因此其安装也分为全局和本地，全局安装命令： `npm install plugin_name` 本地安装则可以使用 GitBook 自带的命令 `gitbook install` 完成，将自动安装 book.json 中所配置的插件，不需要任何参数，默认的安装目录是文档目录下的 ./node_modules/ 目录。

### 文件列表
* [Introduction](README.md)
* [javasript](javasript/README.md)
    * [观察者模式](javasript/观察者模式.md)
    * [事件循环](javasript/eventloop.md)
    * [深拷贝](javasript/deepclone.md)
    * [Polyfill方案](javasript/polyfill.md)
    * [深入理解JSON](javasript/json.md)
    * [proto和prototype](javasript/prototype.md)
    * [函数继承](javasript/inherit.md)
    * [js常用继承，多继承](javasript/extends.md)
    * [es6-class继承使用和原理](javasript/es6-class.md)
* [ES6](es6/README.md)
    * [notice](es6/notice.md)
    * [模块加载](es6/moduleupload.md)
    * [CMD/AMD/ES module](es6/cmd_amd.md)
    * [异步函数](es6/asynchronous.md)
    * [async函数](es6/async.md)
    * [promise](es6/promise.md)
    * [set结构](es6/Set遍历操作.md)
    * [.../reset](es6/扩展运算符…和rest参数.md)
    * [es6解构赋值](es6/ES6的结构赋值.md)
* [node](node/README.md)
    * [node测试](node/test.md)
    * [babel](node/babel.md)
    * [Buffer/Stream/fs](node/stream.md)
* [babel](babel/README.md)
    * [babel常用插件](babel/commonuse.md)
* [webpack](webpack/README.md)
    * [webpack-dev-server](webpack/dev.server.md)
* [vue框架](vue/README.md)
    * [一些常见问题](vue/questions.md)
    * [vue修饰符](vue/xiushifu.md)
    * [vuex记录](vue/vuex.md)
* [html/DOM/css](htmldom/README.md)
    * [动画](css/animation.md)
    * [粘性页脚](htmldom/sticky-footer.md)
* [mobile](mobile/README.md)
    * [移动适配方案](mobile/shipei.md)
    * [mobile适配](mobile/mobileAdaptive.md)
    * [mobile-1px](mobile/mobile1px.md)
    * [css-learn](mobile/csslearn.md)
* [浏览器如何工作](browser/README.md)
    * [浏览器加载渲染流程](browser/bworwseradd.md)
    * [http/http2/https](browser/http-networking.md)
    * [浏览器多线程和js单线程](browser/thread.md)
* [工作积累](work/README.md)
    * [编程小技巧1](work/work1.md)
    * [编程技巧-类型转换](work/work2.md)
    * [JS编程中常见的错误](work/errorlist.md)
    * [git](work/git学习资料.md)
    * [window进程查询](work/windowport.md)
    * [web安全问题](work/safely.md)
    * [图片三种表现方式](work/imgways.md)
    * [web页面图片加载优化](work/imgload.md)
    * [文件下载](work/upload.md)
* [jQuery源码](jquery/README.md)
* [map](map/README.md)


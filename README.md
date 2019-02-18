# mybook
学习笔记电子书，是用 Gitbook 的来整理，使以前学到的知识点不再零散。

### gitbook 使用流程
```shell
安装  
yarn global add gitbook-cli

在目录中
git init

编辑的时候查看 
gitbook serve [--prot xxxx]

最后打包的
gitbook build
```

### 快速生成目录结构，可以使用命令行的方式
1. 直接在 SUMMARY.md 文件中建立好文件的目录
2. 在命令行中执行 `gitbook init` 就会更具你写的目录自动生成响应的文件
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
* [design-mode](designmode/README.md)
    * [观察者模式](designmode/观察者模式.md)
* [accumulation](accumulation/README.md)
    * [深拷贝](accumulation/deepclone.md)
    * [函数继承](accumulation/inherit.md)
    * [git](accumulation/git学习资料.md)
    * [window进程查询](accumulation/windowport.md)
    * [网页加载优化](accumulation/加载优化.md)
* [node](node/README.md)
    * [node测试](node/test.md)
* [webpack](webpack/README.md)
    * [webpack-dev-server](webpack/dev.server.md)
* [ES6](es6/README.md)
    * [notice](es6/notice.md)
    * [模块加载](es6/moduleupload.md)
* [mobile](mobile/README.md)
    * [mobile适配](mobile/mobileAdaptive.md)
    * [mobile-1px](mobile/mobile1px.md)
    * [css-learn](mobile/csslearn.md)
* [协议族](networkingstack/README.md)
    * [http/http2/https](networkingstack/http-networking.md)


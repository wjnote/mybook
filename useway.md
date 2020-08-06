# github 的本地使用流程

```shell
# 安装
yarn global add gitbook-cli

# 在目录中初始化：  创建README.md / SUMMARY.md 两个文件
gitbook init

# 根目录 book.json 里面一般会有plugins插件,直接下载所有插件和主题
gitbook install ./

# 一些常用的命令：
gitbook init  # 初始化目录文件
gitbook build  # [书籍路径] [输出路径] //构建书籍，生成静态网页，默认输出到 _book 目录
gitbook serve [--port 端口号]  # 包含build命令，生成静态网页并运行服务器
gitbook pdf ././mybook.pdf   # 生成 pdf/epub/mobi 格式的电子书

gitbook --version  # 查看版本
gitbook help  # 列出gitbook所有的命令
gitbook --help  # 输出gitbook-cli的帮助信息
gitbook ls # 列出本地所有的gitbook版本
gitbook ls-remote # 列出远程可用的gitbook版本
gitbook fetch 标签/版本号  # 安装对应的gitbook版本
gitbook update  # 更新到gitbook的最新版本
gitbook uninstall 2.0.1  # 卸载对应的gitbook版本
```


## 项目初始化
1. gitbook-cli 和 gitbook 是两个软件 
2. 直接在 SUMMARY.md 文件中建立好文件的目录
3. 在命令行中执行 `gitbook init` 会根据目录自动生成相应文件
4. 但是不会覆盖你自己建立的文件目录结构
> gitbook-cli 会将下载的 gitbook 的不同版本放到 ~/.gitbook中, 可以通过设置GITBOOK_DIR环境变量来指定另外的文件夹


## readme.md 中树状图的生成
这不是markdown的语法，而是通过tree命令生成的，Linux下的tree命令很强大，支持很多参数，但是在window上支持的很少
> macOS 默认不支持tree命令，可以通过homebrew安装，`brew install tree -g`

- /F 显示每个文件夹中文件的名称。（带扩展名） **工作中最常用命令**
- /A 使用 ASCII 字符，而不使用扩展字符。
- `>tree.txt` 可以将树状图的结构输出为当前目录下的 txt 文件

> 在 .md 文件中树状图可以放在 ```html 格式中，放在js格式中会报错


## gitbook 插件系统
GitBook默认带有 5 个插件：`highlight、search、sharing、font-settings、livereload` 如果要去除自带的插件，可以在插件名称前面加 '-'。  Gitbook目前一共有700个左右的插件，如果要使用其他插件，可以在plugins中添加插件名称，然后在pluginsConfig中配置插件。

> 如果在执行build或server命令时提示要使用的插件不存在，只需在终端输入 gitbook install ./ 即可自动安装缺少的插件。如果要指定插件的版本，可以使用plugin@0.3.1

GitBook 插件本质上和 Node.js 包一样，因此其安装也分为全局和本地，全局安装命令： `npm install plugin_name` 本地安装则可以使用 GitBook 自带的命令 `gitbook install` 完成，将自动安装 book.json 中所配置的插件，不需要任何参数，默认的安装目录是文档目录下的 `./node_modules/` 目录。

[插件参考博客](https://segmentfault.com/a/1190000019806829)


## 启动之后热更新
在使用中启动服务 `gitbook serve` 之后，如果修改本地文件，则会停止服务并报错，错误提示表示没有权限，有一种折中的办法就是启动之后，将该目录下的 `_book` 文件夹删除，然后再修改就可以实现热更新


## 启动的时候报错
如果在打包或者启动的时候报错 `Error: ENOENT: no such file or directory, stat 'C:\Users\fi\GitBook\Library\Import\multidichelpproject\_book\gitbook\gitbook-plugin-lunr\lunr.min.js'` 其实文件夹是有文件的，则可能是 `gitbook` 的一个问题，

解决办法是：  `C:\Users\用户\.gitbook\versions\3.2.3\lib\output\website` 下找到 `copyPluginAssets.js` 文件 将第67和第112行 `confirm: true` 改为 `confirm: false` 即可运行。


## book.json 中常用插件注释
### 默认插件
- sharing：默认的分享插件。
- fontsettings：默认的字体、字号、颜色设置插件。
- search：默认搜索插件。
- highlight：默认的代码高亮插件，通常会使用 prism 来替换。
- lunr

### 常用插件
- tbfed-pagefooter ：自定义页脚，显示版权和最后修订时间。
- book-summary-scroll-position-saver：自动保存左侧目录区域导航条的位置。
- atoc ： 插入 TOC 目录。
- ace ： 插入代码高亮编辑器。
- expandable-chapters ： 收起或展开章节目录中的父节点。
- expandable-chapters-small : 可扩展导航章节，左侧的导航条可以折叠,  使用的更小的箭头
- anchors ： 标题带有 github 样式的锚点。
- github ： 在右上角显示 github 仓库的图标链接。
- github-buttons ： 显示 github 仓库的 star 和 fork 按钮。
- `-sharing` : 前面的 '-' 表示去掉Gitbook自带的插件功能
- splitter ： 侧边栏宽度可调节
- lightbox ： 单击查看图片
- page-toc-button : 悬浮目录， 右侧展示整个页面的导航按钮
- back-to-top-button ： 回顶部的按钮
- chapter-fold : 导航目录折叠
- copy-code-button : 代码复制按钮
- sharing-plus : 分享当前页面，比默认的 sharing 插件多了一些分享方式
- anchor-navigation-ex : 导航扩展，增加锚点，返回顶部，显示序号
- favicon : 浏览器标签栏的favicon图标,在 pluginsConfig 配置中配置具体的图标路径
- simple-page-toc : 生成本页目录,需要在文章中插入标签
- ad ： 在每个页面顶部和底部添加广告或任何自定义内容。


---

[参考博客](https://www.jianshu.com/p/427b8bb066e6)


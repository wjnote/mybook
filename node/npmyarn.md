## 包管理工具

现在包管理工具更新之后有了一个 `package-lock.json` 文件是锁定包的版本和依赖关系的，我们拉取别人的项目的时候可以下载固定的包，不会由于版本不兼容启动不起。

### npm ci
有时候2台电脑之间由于npm版本不一致，拉取下来的包 `package-lock.json` 会有不同，或者是出现一些错误，想要下载和别人一样的包 只需要执行 `npm ci` 命令进行安装，此命令会比`npm i`下载速度更快

- 该项目必须具有现成的 `package-lock.json`或 `npm-shrinkwrap.json` 文件
- 已经存在`node_modules`,则会删除该文件夹再下载包
- 不会写入`package.json` 或执行任何软件包锁
- 只能初始化项目，不能加载单个包

> 使用npm ci 部署的项目依赖包会一直不变

### 常用的node包

`npm-check`  检测过时的，未使用，不安全的依赖包

### npm

#### npm 基本配置
`npm config get registry` 可以查看本地配置的npm包的源路径， 也可以使用set命令设置

> 本地全局环境的包源地址可以在 `~/.npmrc` 文件查看，如果项目中 `.npmrc` 文件，下载包的时候就会优先使用 `.npmrc` 配置文件中的路径

> 可以安装 nrm 包来快速查询，设置包的源地址

#### npm install 的执行过程
1. 发出`npm install` 命令
2. npm 向 registry 查询模块压缩包的网址
3. 下载压缩包, 存放到` ~/.npm`(本地npm缓存目录)
4. 解压压缩包到当前项目的 `node_modules` 目录

可以看到安装一个模块后，本地保存了2分，一份是缓存数据，一份是本项目的解压代码，运行` npm i `只会检测当前文件夹`node_modules` 目录，而不是检测 `~/.npm`

有些项目重新下载包也不能启动项目的时候，可以删除`package-lock.json`版本锁定文件，再执行` npm cache clean -f ` 来清除缓存，然后再重新下载包。

#### npm常用命令

`npm -h`  npm命令提示

`npm config get[set] registry`  获取/设置npm包的地址源

`npm info XXX`  可以查看某个包的信息，GitHub地址等信息

`npm ls XXX [-g]`  可以查看本机安装的某个包，也可能没有安装

`npm install XXX[@N]`  安装某个包

`npm install [-g] XXX [--dev]`  安装包

`npm uninstall [-g/-S/-D] XXX`  卸载包

`npm update [-g] XXX `  更新包

`npm list [-g] --depth 0`  查询全局安装了那些包

`which node`  展示node安装位置，`node_global`就是全局安装的包

`npm view XXX`  查看某一个包的信息，最新版本，下载的registry地址

`npm root -g`  查看包的对安装路径/全局安装路径

`npm outdated [XXX]`  查看过时的npm包信息

`npm view XXX versions`  查看包的所有版本信息



### yarn
yarn是一个更快的包管理工具，操作流程和npm类似，其中下载包的地址都是境内，所以速度比较块

#### yarn配置相关
1. 安装，直接在官网下载安装包，安装的时候可以选择不默认为C盘
2. yarn 自定义路径配置
    1. 首选查看 yarn 的全局执行路径 `yarn global bin`
    2. 然后修改全局的执行路径 `yarn config set prefix e:\yarn\node_global`
    3. 修改 yarn 的全局脚本安装路径
    4. 首先在命令行中 配置 `yarn config set global-folder e:\yarn\node_global\global`
    5. 然后再用户目录下找到 `.yarnrc` 文件，将找到文字 `global-folder` 修改为 `--global-folder`
3. 修改全局配置之后，全局安装的包都不能用了，需要修改全局变量，将执行路径添加到 path（系统变量），利用 `yarn global bin` 来获取

#### yarn常用命令
`yarn config list`  查询全局的配置信息

`yarn global bin`  首选查看全局执行路径

`yarn [global] add XXX [--dev]`  安装包

`yarn [global] remove XXX` 卸载包,项目中开发依赖和打包依赖都可以删除

`yarn upgrade XXX`  更新包

`yarn global list`  查询全局安装的包

`yarn global dir`  全局安装的包路径，文件夹有一个 `package.json` 其中的 `dependencies` 就是全局安装的包


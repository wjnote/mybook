
### git学习资料
1. 下载git安装文件 [官网](https://git-scm.com/downloads)
2. 使用之前设置一下用户的信息,在命令行输入
```shell
// 设置用户信息 如果设置的时候没有后面的参数 默认第一个
$ git config --local // 只对某个仓库有效
$ git config --global // global对当前用户所有仓库有效 **工作中最常用**
$ git config --system // 对系统所有登录用户有效
// 如果想查看之前的设置
$ git config --list --local
$ git config --list --global
```
3. 在项目中初始化 `git init` / `git init projectName` ，会出现一个 .git 文件夹
> 上面两种命令是两种不同的情况，第一个是在已经存在文件的时候，第二种是在全新的文件下

4. 往仓库里添加文件，是本地文件先添加到暂存区中，被Git监控，再commit上 ，注意其中 `git status` 命令可以查看目前暂存区的状态(那些没监控，那些添加了没提交)，如果是空文件则添加暂存无效
5. 提交完成之后， `git log` 可以查看提交信息，用户和提交的文件


##### git常用命令
```shell
git init ： 初始化 git 项目，然后就需要配置最小的配置参数
git status ： 当前文件的 git 状态
git log ： 展示git的日志
git add <filename> ： 添加文件到暂存区
git rm <filename> ： 删除文件
git rm --cache <filename> : 删除暂存区文件
git commit -m'explain' ： 添加某个文件到版本库 后面是注释信息
git commit -u ： 添加所有未提交的文件到版本库
git reset <filename>： 删除暂存区的文件，   git  add 相反操作
*** git reset --hard :  git暂存区中所有的改动都会被清理到
```
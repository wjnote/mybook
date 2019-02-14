### git学习资料
1. 下载git安装文件 [官网](https://git-scm.com/downloads)
2. 使用之前设置一下用户的信息,在命令行输入,如果使用了global配置，在本机配置一次就行了，之后会自动去读取
```shell
// 设置用户信息 如果设置的时候没有后面的参数 默认第一个
$ git config --local // 只对某个仓库有效
$ git config --global // global对当前用户所有仓库有效 **工作中最常用**
$ git config --system // 对系统所有登录用户有效
// 如果想查看之前的设置
$ git config --list --local
$ git config --list --global
```
3. 在项目中初始化 `git init projectName` / `git init ` ，会出现一个 .git 文件夹
> 上面两种命令是两种不同的情况，第一个是在全新的文件下，第二种是在已经存在文件的文件夹中初始化Git仓库来版本控制，应该开始跟踪这些文件并提交。 你可通过 git add 命令来实现对指定文件的跟踪,然后`git commit` 来提交

4. 往仓库里添加文件，是本地文件先添加到暂存区中，被Git监控，再commit上 ，注意其中 `git status` 命令可以查看目前暂存区的状态(那些没监控，那些添加了没提交)，如果是空文件则添加暂存无效
5. 提交完成之后， `git log` 可以查看提交信息，用户和提交的文件


##### git常用命令
```shell
git init ： 初始化 git 项目，然后就需要配置最小的配置参数，不是必须的，会自动使用全局配置的参数
git add <filename> ： 开始跟踪一个文件(使用文件或目录的路径作为参数) / 把已跟踪的文件放到暂存区 / 合并时把有冲突的文件标记为已解决状态
git status ： 当前文件的 git 状态
git log --oneline： 展示git的日志
git rm <filename> ： 删除文件
git rm --cached <filename> : 删除暂存区文件,但是保留在当前工作目录中,并且git之后都不再跟踪该文件(和 .gitignore作用一样)
git commit -m'explain' ： 添加某个文件到版本库 后面是注释信息
git commit -a ： 添加所有未提交的文件到版本库
git commit -a -m"explain": 产生的内容不存入暂存区，直接提交到commit中去。不推荐使用
git reset <filename>： 删除暂存区的文件，   git  add 相反操作
*** git reset --hard :  git暂存区中所有的改动都会被清理到

分支信息：切换到新分支上时 Git 会重置你的工作目录，使其看起来像回到了你在那个分支上最后一次提交的样子。 
Git 会自动添加、删除、修改文件以确保此时你的工作目录和这个分支最后一次提交时的样子一模一样。
git branch -a : 可以查看当前工作在那个分支下面，个人文件一般都是 master 分支
git branch -v  可以查看当前各个分支的状态，也可以查看当前工作在那个分支下
git checkout <name> : 切换到某个分支
git branch -b <name>  新建分支并切换到该分支上开发
git log --oneline --decorate  可以查看各个分支当前所指的对象
git log --oneline --decorate --graph --all  输出你的提交历史、各个分支的指向以及项目的分支分叉情况。
```

### git中 comit  tree   blob 的关系
![commit/tree/blob关系图](../resouce/imgs/git04.png)
- 一个commit代表一柯树，表示此刻文件的所有快照
- 树中也可以包含其他树结构
- 树中也包含了 blob（就是具体的文件）
- blob 和文件名无关，相同的内容是同一个blob


### 理解其中分支的概念和运行在分支上的作用
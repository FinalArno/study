## git 使用教程

> git 是全球最大的分布式版本控制系统（没有之一）

### git config --global user.name "用户名"

配置用户姓名

### git config --global user.email "用户邮箱"

配置用户邮箱

### git init

初始化 git 仓库

### git add xxx.txt /git add \*

添加需要提交的 xxx.txt 文件，或者添加所有需要提交的文件到缓冲区

### git commit -m "提交信息"

提交到本地 git master 分支，（git init 的时候会自动创建一个 master 分支） 加上 -m 表示提交信息，不加会进入文本编辑界面

### git status

git status 命令可以让我们时刻掌握仓库当前的状态

### git diff/git diff 文件名

查看具体修改了哪些行

### git log

查看最近到最远的提交日志(从上到下),可以加上 --pretty=oneline 来使日志显示成一行

### git reflog

查看命令历史,以便确定要回退到未来哪个版本

### git reset --hard 版本号

回退到指定版本号

### git reset --hard HEAD^^^^^

回退到前面的第^个版本好，如果不想^符号太多，就使用 HEAD~99,表示回到前面第 99 个版本

### git checkout -- 文件名 让该文件回到 commit 或者 add 后的状态,如果不加-- 则表示切换分支

把该文件工作区的修改全部撤销，这里有两种情况
1，该文件自从被修改了还没有进行 git add ,那么撤销修改就和版本库一模一样（commit）
2，该文件已经 git add 以后又进行了修改，那么撤销只会撤销到 git add 后的状态（add）

### git checkout 其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

> 场景 1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令 git checkout -- file。

> 场景 2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令 git reset HEAD <file>，就回到了场景 1，第二步按场景 1 操作。

### git reset HEAD 文件名

可以把 git add 撤销，即把暂缓区的修改撤销，重新放回工作区，（就是修改还在，但是撤销了 git add）

## github 使用教程

1，先登陆 github，创建一个仓库，然后复制链接,git clone xxx.git 把仓库代码克隆到本地，然后进行开发，git add \*添加到暂缓区，git commit 提交到本地，然后 git push origin master 提交到主分支
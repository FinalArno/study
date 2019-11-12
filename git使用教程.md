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

> 场景 1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令 `git checkout -- file`。

> 场景 2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令 `git reset HEAD <file>`，就回到了场景 1，第二步按场景 1 操作。

### git reset HEAD 文件名

可以把 git add 撤销，即把暂缓区的修改撤销，重新放回工作区，（就是修改还在，但是撤销了 git add）

### 配合 github 使用教程

1，先登陆 github，创建一个仓库，然后复制链接,`git clone xxx.git` 把仓库代码克隆到本地，然后进行开发，`git add *`添加到暂缓区，git commit 提交到本地，然后 `git push origin master` 提交到主分支
2，`git checkout -b dev`,创建并切换到 dev 分支，然后修改内容，`git add *`，`git commit`到 dev 分支，这时候有两个选择：

- 1⃣ 如果不需要把 dev 分支提交到 github 远程仓库上，那么直接`git checkout master`切换到 master 分支，然后 `git merge dev`，将 dev 修改的内合并到 master 分支
- 2⃣️ 如果需要将 dev 分支提交到 github 远程仓库，那么直接使用 `git push origin dev` 就可以将 dev 分支提交到 github 远程仓库并在 github 上创建 dev 分支

### git checkout <branch>/ git switch <branch>

切换到某个分支，由于`git checkout <branch>` 与`git checkout -- <file>`容易产生歧义，后来新增了`git switch <branch>`

### git switch -c <branch>

创建并切换到某分支(当前 mac 不支持这个命令)

### git branch /git branch <branch>/ git branch -d <branch>

查看所有分支/创建一个分支/删除某个分支

### git merge <branch>

合并<branch>分支到当前分支

### git log --graph

查看分支合并图

### git merge --no-ff -m 'xxxx' <branch>

合并的时候提交信息，可以保留合并的分记录(可以看出合并记录，ff 表示 fast-forward 模式,方便回退)

### git stash

存储现场的工作，然后切换到其他分支去修改 bug，修改好了提交合并 push，然后返回当前分支使用 git stash list 查看当前存储的列表,
可以用 git stash pop 恢复存储到工作区，并且删除 git stash list 的内容（即删除存储区）

### git --cherry-pick <commit>

复制一个特定的提交到当前分支,

> 其使用条件：如果当前在 dev 分支开发，线上有一个 bug，切换到 master（线上分支）分支后，从 master 分支创建一个 issue-01 分支，然后修改 bug，提交，切换到 master 分支合并 issue-01 分支，并删除 issue-01 分支，此时切换到 dev 分支继续开发，但是想要把修改的 bug 也合并到当前分支，就用--cherry-prick 版本号 来使得 dev 分支也有 issue-01 分支修改后的内容

### git branch -D <branch>

删除一个还没有合并过的分支，-D 是强制删除的意思，-d 是普通删除（不能删除未合并的分支）

### git remote /git remote -v

查看远程仓库信息/查看远程仓库详细信息

### git branch --set-upstream-to=origin/dev dev

如果 git pull 失败，则说明本地分支和远程分支的链接关系没有创建，原因是没有指定本地 dev 分支与远程 origin/dev 分支的链接，根据提示，设置 dev 和 origin/dev 的链接：

### git checkout -b <branch-name> origin/<branch-name>

在本地创建和远程分支对应的分支

### git pull

从远程仓库获取分支

### git rebase

- rebase 操作可以把本地未 push 的分叉提交历史整理成直线；
- rebase 的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。

### git tag /git tag <版本号>/git tag <版本号> <commit>

查看当前分支的 tag/为当前分支添加 tag/从指定的提交处打一个 tag

### git tag -a <版本号> -m <提交信息>

为当前分支添加 tag 并且指定标签信息

### git show <版本号>

查看该 tag 的提交信息

### git tag -d <版本号>

删除指定版本的 tag 标签

### git push origin :refs/tags/<版本号>

删除远程仓库指定版本号的 tag

### git push origin <tagname>/git push origin --tags

推送某个标签到远程仓库/推送所有标签到远程仓库

### git remote rm origin

删除已有的远程仓库关联

### git remote add origin xxx.git

添加远程仓库关联

## 从本地 git 上传到 github 仓库（不设置 ssh）

- git remote add origin https://github.com/FinalArno/testgit.git(把本地仓库与远程github关联起来)
- git pull --rebase origin master（貌似是这一步让这个玩意可以 push）
- git push -u origin master（第一次 push 才需要加 -u）
- 大功告成

### 忽略某些文件时，需要编写.gitignore

[官方配置文件地址]('https://github.com/github/gitignore')

### ssh-keygen -t rsa -C "863780387@qq.com"

> 由于你的本地 Git 仓库和 GitHub 仓库之间的传输是通过 SSH 加密的，所以，需要一点设置

- 创建密钥对，一路回车，如果一切顺利的话，可以在用户主目录里找到.ssh 目录，里面有 id_rsa 和 id_rsa.pub 两个文件，这两个就是 SSH Key 的秘钥对，id_rsa 是私钥，不能泄露出去，id_rsa.pub 是公钥，可以放心地告诉任何人
- 登陆 GitHub，打开“Account settings”，“SSH Keys”页面：然后，点“Add SSH Key”，填上任意 Title，在 Key 文本框里粘贴 id_rsa.pub 文件的内容：

### git log --abbrev-commit
显示简洁的提交版本号
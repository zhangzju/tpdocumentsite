## 本地仓库与远程仓库

本地仓库与远程仓库是git相对于传统的集中式版本控制系统的最大区别之一。

Git是分布式版本控制系统，同一个Git仓库，可以分布到不同的机器上。为了能够维持不同的仓库之间的的版本既可以保持随时同步又不至于大规模出现冲突，每一个仓库都具有自己的独立的分支。

所以对于每一个workspace来说，本地的分支和远程同名的分支是两个分支，但是这两个分支在进行仓库之间的同步操作的时候，会被认为是相关联的，他们的数据也会同步，详见[git branch及命令行中的分支管理](/doc/#/gitlab/git_branch_textmode)。

### git的区域划分

在之前的文档中可以了解到git所有的文件会被划分到四种区域：Workspace,Index(Staged),Repository,Remote.他们之间的关系如下图：

![git_local_work](imgs/git_local_work.png)

### Workspace到Staged

### git add

git add操作是将一个处于工作区的文件添加到暂存区，这个操作是一次性的，他会将现有的更改添加到暂存区中，等待下一次提交的时候添加到仓库中去。

```ahell
# 添加指定文件到暂存区
　git add [file1] [file2] ...

# 添加指定目录到暂存区，包括子目录
　git add [dir]

# 添加当前目录的所有文件到暂存区
　git add .

# 添加每个变化前，都会要求确认
# 对于同一个文件的多处变化，可以实现分次提交
　git add -p

```

需要注意的是，当你进行暂存之后，如果对文件进行了修改，在暂存之后的修改是不会被提交的！！！

也是因为这个特性，才构成我们针对嵌入式开发的独特的控制流程。

### git status

命令可以列出当前目录所有还没有被git管理的文件和被git管理且被修改但还未提交(git commit)的文件。

此时会出现类似如下的显示：

```shell
➜  ansible git:(master) ✗ git status
位于分支 master
您的分支与上游分支 'origin/master' 一致。
尚未暂存以备提交的变更：
  （使用 "git add <文件>..." 更新要提交的内容）
  （使用 "git checkout -- <文件>..." 丢弃工作区的改动）

	修改：     backupGitlab_fetch.yml

修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）
```
git status有一个额外的参数，可以只列出所有已经被git管理的且被修改但没提交的文件。

```shell
git status -uno
```

### git rm

git rm操作可以将我们的删除操作放到暂存区，即我们删除了一些原本处于工作区的文件，这个删除操作本身会被暂存;同时，这个命令也可以将处于暂存区的文件放回到工作区，可以看做git add的反向操作。

```shell
# 删除工作区文件，并且将这次删除放入暂存区
　git rm [file1] [file2] ...

# 停止追踪指定文件，但该文件会保留在工作区
　git rm --cached [file]
```
### git mv

git mv类似git rm的第一种作用，将一个更改文件名的动作放入暂存区。

```shell
# 改名文件，并且将这个改名放入暂存区
　git mv [file-original] [file-renamed]
```

## Staged到Repository

### git commit 

git commit命令的作用是将处于暂存区的操作提交到本地仓库。

```shell
# 提交暂存区到仓库区
git commit -m [message]

# 提交暂存区的指定文件到仓库区
　git commit [file1] [file2] ... -m [message]

# 提交工作区自上次commit之后的变化，直接到仓库区
　git commit -a

# 提交时显示所有diff信息
　git commit -v

# 使用一次新的commit，替代上一次提交
# 如果代码没有任何新变化，则用来改写上一次commit的提交信息
　git commit --amend -m [message]

# 重做上一次commit，并包括指定文件的新变化
　git commit --amend [file1] [file2] ..
```
关于commit的信息如何填写，假如在git commit中没有添加-m参数，那么会进入到一个命令行界面的编辑器中，此时，你可以编写多行的文本。

关于commit信息的规范，参照之前SVN的做法以及台湾团队的实践，我们需要写明这次提交解决的问题，例如以下几项：

* Bugzilla中的bug序号以及描述
* 这个分支所要完成的功能的某个部分
* 这次提交修改的部分会带来的影响


### git log

git  log用于查看仓库中所有的”从暂存区提交到仓库“的操作。

```shell
# 显示所有的提交信息，但是忽略掉merge信息(merge操作用于合并分支，也属于从暂存区到仓库的操作)
git log --no-merges

# 显示所有include/scsi 或者 drivers/scsi路径下的提交(和这些子路径下的文件相关的提交)
git log include/scsi drivers/scsi

# 显示最近两周的提交
git log --since="2 weeks ago" 

# 显示所有和文件builtin/rev-list.c有关的提交，包括这个文件被更改文件名之前的操作
git log --follow builtin/rev-list.c

# 显示所有只在本地的提交，不显示origin远程分支的提交
git log --branches --not --remotes=origin

# 显示master分支上的所有提交，不包括远程仓库上同名分支的提交
git log master --not --remotes=*/master

# 显示所有和main.c文件中的main()函数相关的提交，功能很强大
git log -L '/int main/',/^}/:main.c

# 显示最近的三次提交
git log -3

```

在针对commit进行操作的时候，需要获取每一次提交的<HASH>，一般都是通过git log获取，不过完整的<HASH>是一串很长的字符串，git提供了一个简短的字符串来标记每一次提交的<HASH>，可与通过以下命令获取：

```shell
git log --oneline
```
这个命令极大的简化了log的信息，将每次的提交都简化为一行，以简短的<HASH>开始，跟着提交的信息，非常方便。

### git checkout

git checkout有两种用法：

* 切换不同的分支
* 将仓库中的文件同步到工作区，

前一种用法详见[git branch及命令行中的分支管理](/doc/#/gitlab/git_branch_textmode)，后一种用法一般的使用场景是类似于svn update，当你的文件有缺失的时候，可以通过这个命令来恢复。

<strong>注意：当你在工作区的修改没有被暂存的时候，可以用这个命令来进行丢弃，但是已经被暂存的修改是无法通过checkout来丢弃的，你需要git reset</strong>

```shell
# 从仓库中恢复hello.c文件到暂存区，注意这里hello.c之前有个空格，如果没有这个空格，你会切换到一条名为hello.c的分支！！！
git checkout -- hello.c

# 恢复暂存区的所有文件到工作区
git checkout .

# 恢复某个commit的指定文件到暂存区和工作区，这里的[commit]即是之前提到的<HASH>
 git checkout [commit] [file]

```

### git reset

git reset 常用于将工作区以及暂存区的文件恢复到仓库中的版本，是比较常用的回退操作。

```shell
# 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变
 git reset [file]

# 重置暂存区与工作区，与上一次commit保持一致
 git reset --hard

# 重置当前分支的指针为指定commit，同时重置暂存区，但工作区不变
 git reset [commit]

# 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致
 git reset --hard [commit]

# 重置当前HEAD为指定commit，但保持暂存区和工作区不变
 git reset --keep [commit]
```

## Repository到Remote

本地仓库到远程仓库的操作是分布式系统的重要组成部分，也是分布式开发和渐进式gitlab workflow的关键之处。遵循gitlab workflow能够合理高效的进行大规模的开发。

一个本地仓库可以有多个远程仓库，因为git并没有中心，所有的仓库都是平等的。不过在团队开发中，gitlab服务器上的仓库应当作为一个集中式的核心仓库，代表着输出的成果。



### git remote

git remote用于管理远程仓库。

尽管在我们内部开发的时候，只需要一个远程仓库，即gitlab服务器上的仓库，但是还是要根据git的分布式方法进行管理，为这个远程仓库添加一个别名：

```shell
# 增加一个新的远程仓库，并命名
 git remote add [shortname] [url]
```
由于我们在开发过程中基本上都是通过git clone来获取代码，在clone操作之后，本地的代码的远程仓库已经配置好，gitlab上的仓库会被命名为origin.

有时候会遇到我们的项目更改名字或者移交到其他的group的问题，此时没有必要重新下载一份全新的代码，我们只需要更改origin这个远程仓库的地址，即可实现无缝切换：

```shell
# 这里的[url]应当替换为服务器上最新的，你所需要获取代码的项目的地址
git remote set-url origin [url]
```
### git push

git push的作用是将本地仓库的操作同步到远程仓库。

```shell
# 按照我们既定的开发流程，这个命令即可实现我们的提交要求
git push

# 上传本地指定分支到远程仓库，这个命令可以用于仓库的迁移
 git push [remote] [branch]

# 强行推送当前分支到远程仓库，即使有冲突,这个操作一般用于你将错误的更改提交到了远程仓库，尽量不要使用
 git push [remote] --force

```

### git pull

git pull的作用是将远程仓库中的操作同步的本地仓库，可以看作是git push的反方向操作。

```shell
# 取回远程仓库的变化，与当前分支合并，按照我们的workflow,需要大家在develop下进行这项操作
 git pull

# 取回远程仓库的变化，并与本地分支合并，这个指令制定了要取回的分支，一般用于需要在服务器上新建不同仓库的场景
 git pull [remote] [branch]
```

### git fetch

git fetch用于获取远程仓库中与本地仓库差异的部分，而不是将本地仓库更新为远程仓库。

```shell
# git fetch获取的更新会被放置到工作区，一般用于在自己的分支获取其他同事正在开发的还未合并的分支上的更改，然后选择性的合并
git fetch remote_repo remote_branch_name:local_branch_name 
```


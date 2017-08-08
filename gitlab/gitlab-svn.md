## Git 与 Subversion

当前，大多数开发中的开源项目以及大量的商业项目都使用 Subversion 来管理源码。作为最流行的开源版本控制系统，Subversion 已经存在了接近十年的时间。它在许多方面与 CVS 十分类似，后者是前者出现之前代码控制世界的霸主。

Git 最为重要的特性之一是名为 git svn 的 Subversion 双向桥接工具。该工具把 Git 变成了 Subversion 服务的客户端，从而让你在本地享受到 Git 所有的功能，而后直接向 Subversion 服务器推送内容，仿佛在本地使用了 Subversion 客户端。也就是说，在其他人忍受古董的同时，你可以在本地享受分支合并，使暂存区域，衍合以及 单项挑拣等等。这是个让 Git 偷偷潜入合作开发环境的好东西，在帮助你的开发同伴们提高效率的同时，它还能帮你劝说团队让整个项目框架转向对 Git 的支持。这个 Subversion 之桥是通向分布式版本控制系统（DVCS, Distributed VCS ）世界的神奇隧道。

### git svn
Git 中所有 Subversion 桥接命令的基础是 git svn 。所有的命令都从它开始。相关的命令数目不少，你将通过几个简单的工作流程了解到其中常见的一些。

值得警戒的是，在使用 git svn 的时候，你实际是在与 Subversion 交互，Git 比它要高级复杂的多。尽管可以在本地随意的进行分支和合并，最好还是通过衍合保持线性的提交历史，尽量避免类似与远程 Git 仓库动态交互这样的操作。

避免修改历史再重新推送的做法，也不要同时推送到并行的 Git 仓库来试图与其他 Git 用户合作。Subersion 只能保存单一的线性提交历史，一不小心就会被搞糊涂。合作团队中同时有人用 SVN 和 Git，一定要确保所有人都使用 SVN 服务来协作——这会让生活轻松很多。

### 初始设定

为了展示功能，先要一个具有写权限的 SVN 仓库。如果想尝试这个范例，你必须复制一份其中的测试仓库。比较简单的做法是使用一个名为 svnsync 的工具。较新的 Subversion 版本中都带有该工具，它将数据编码为用于网络传输的格式。

要尝试本例，先在本地新建一个 Subversion 仓库：

```shell

$ mkdir /tmp/test-svn
$ svnadmin create /tmp/test-svn

```

然后，允许所有用户修改 revprop —— 简单的做法是添加一个总是以 0 作为返回值的 pre-revprop-change 脚本：

```shell

$ cat /tmp/test-svn/hooks/pre-revprop-change
#!/bin/sh
exit 0;
$ chmod +x /tmp/test-svn/hooks/pre-revprop-change

```
现在可以调用 svnsync init 加目标仓库，再加源仓库的格式来把该项目同步到本地了：

```shell

$ svnsync init file:///tmp/test-svn http://progit-example.googlecode.com/svn/

```

这将建立进行同步所需的属性。可以通过运行以下命令来克隆代码：

```shell

$ svnsync sync file:///tmp/test-svn
Committed revision 1.
Copied properties for revision 1.
Committed revision 2.
Copied properties for revision 2.
Committed revision 3.
...

```

别看这个操作只花掉几分钟，要是你想把源仓库复制到另一个远程仓库，而不是本地仓库，那将花掉接近一个小时，尽管项目中只有不到 100 次的提交。 Subversion 每次只复制一次修改，把它推送到另一个仓库里，然后周而复始——惊人的低效，但是我们别无选择。

### 入门

有了可以写入的 Subversion 仓库以后，就可以尝试一下典型的工作流程了。我们从 git svn clone 命令开始，它会把整个 Subversion 仓库导入到一个本地的 Git 仓库中。提醒一下，这里导入的是一个货真价实的 Subversion 仓库，所以应该把下面的 file:///tmp/test-svn 换成你所用的 Subversion 仓库的 URL：

```shell

$ git svn clone file:///tmp/test-svn -T trunk -b branches -t tags
Initialized empty Git repository in /Users/schacon/projects/testsvnsync/svn/.git/
r1 = b4e387bc68740b5af56c2a5faf4003ae42bd135c (trunk)
      A    m4/acx_pthread.m4
      A    m4/stl_hash.m4
...
r75 = d1957f3b307922124eec6314e15bcda59e3d9610 (trunk)
Found possible branch point: file:///tmp/test-svn/trunk => \
    file:///tmp/test-svn /branches/my-calc-branch, 75
Found branch parent: (my-calc-branch) d1957f3b307922124eec6314e15bcda59e3d9610
Following parent with do_switch
Successfully followed parent
r76 = 8624824ecc0badd73f40ea2f01fce51894189b01 (my-calc-branch)
Checked out HEAD:
 file:///tmp/test-svn/branches/my-calc-branch r76

```

这相当于针对所提供的 URL 运行了两条命令—— git svn init 加上 git svn fetch 。可能会花上一段时间。我们所用的测试项目仅仅包含 75 次提交并且它的代码量不算大，所以只有几分钟而已。不过，Git 仍然需要提取每一个版本，每次一个，再逐个提交。对于一个包含成百上千次提交的项目，花掉的时间则可能是几小时甚至数天。

-T trunk -b branches -t tags 告诉 Git 该 Subversion 仓库遵循了基本的分支和标签命名法则。如果你的主干(译注：trunk，相当于非分布式版本控制里的master分支，代表开发的主线），分支或者标签以不同的方式命名，则应做出相应改变。由于该法则的常见性，可以使用 -s 来代替整条命令，它意味着标准布局（s 是 Standard layout 的首字母），也就是前面选项的内容。下面的命令有相同的效果：

```shell

$ git svn clone file:///tmp/test-svn -s

```
现在，你有了一个有效的 Git 仓库，包含着导入的分支和标签：

```shell

$ git branch -a
* master
  my-calc-branch
  tags/2.0.2
  tags/release-2.0.1
  tags/release-2.0.2
  tags/release-2.0.2rc1
  trunk

```

值得注意的是，该工具分配命名空间时和远程引用的方式不尽相同。克隆普通的 Git 仓库时，可以以 origin/[branch] 的形式获取远程服务器上所有可用的分支——分配到远程服务的名称下。然而 git svn 假定不存在多个远程服务器，所以把所有指向远程服务的引用不加区分的保存下来。可以用 Git 探测命令 show-ref 来查看所有引用的全名。

## 迁移到 Git
如果在其他版本控制系统中保存了某项目的代码而后决定转而使用 Git，那么该项目必须经历某种形式的迁移。本节将介绍 Git 中包含的一些针对常见系统的导入脚本，并将展示编写自定义的导入脚本的方法。

### Subversion

读过前一节有关 git svn 的内容以后，你应该能轻而易举的根据其中的指导来 git svn clone 一个仓库了；然后，停止 Subversion 的使用，向一个新 Git server 推送，并开始使用它。想保留历史记录，所花的时间应该不过就是从 Subversion 服务器拉取数据的时间（可能要等上好一会就是了）。

然而，这样的导入并不完美；而且还要花那么多时间，不如干脆一次把它做对！首当其冲的任务是作者信息。在 Subversion，每个提交者在都在主机上有一个用户名，记录在提交信息中。上节例子中多处显示了 schacon ，比如 blame 的输出以及 git svn log。如果想让这条信息更好的映射到 Git 作者数据里，则需要 从 Subversion 用户名到 Git 作者的一个映射关系。建立一个叫做 user.txt 的文件，用如下格式表示映射关系：

```shell

schacon = Scott Chacon <schacon@geemail.com>
selse = Someo Nelse <selse@geemail.com>

```

通过该命令可以获得 SVN 作者的列表：

```shell

$ svn log ^/ --xml | grep -P "^<author" | sort -u | \
      perl -pe 's/<author>(.*?)<\/author>/$1 = /' > users.txt

```

它将输出 XML 格式的日志——你可以找到作者，建立一个单独的列表，然后从 XML 中抽取出需要的信息。（显而易见，本方法要求主机上安装了grep，sort 和 perl.）然后把输出重定向到 user.txt 文件，然后就可以在每一项的后面添加相应的 Git 用户数据。

为 git svn 提供该文件可以然它更精确的映射作者数据。你还可以在 clone 或者 init后面添加 --no-metadata 来阻止 git svn 包含那些 Subversion 的附加信息。这样 import 命令就变成了：

```shell

$ git svn clone http://my-project.googlecode.com/svn/ \
      --authors-file=users.txt --no-metadata -s my_project

```

现在 my_project 目录下导入的 Subversion 应该比原来整洁多了。原来的 commit 看上去是这样：

```shell

commit 37efa680e8473b615de980fa935944215428a35a
Author: schacon <schacon@4c93b258-373f-11de-be05-5f7a86268029>
Date:   Sun May 3 00:12:22 2009 +0000

    fixed install - go to trunk

    git-svn-id: https://my-project.googlecode.com/svn/trunk@94 4c93b258-373f-11de-
    be05-5f7a86268029

```

现在是这样：

```shell

commit 03a8785f44c8ea5cdb0e8834b7c8e6c469be2ff2
Author: Scott Chacon <schacon@geemail.com>
Date:   Sun May 3 00:12:22 2009 +0000

    fixed install - go to trunk

```
不仅作者一项干净了不少，git-svn-id 也就此消失了。

你还需要一点 post-import（导入后） 清理工作。最起码的，应该清理一下 git svn 创建的那些怪异的索引结构。首先要移动标签，把它们从奇怪的远程分支变成实际的标签，然后把剩下的分支移动到本地。

要把标签变成合适的 Git 标签，运行

```shell

$ git for-each-ref refs/remotes/tags | cut -d / -f 4- | grep -v @ | while read tagname; do git tag "$tagname" "tags/$tagname"; git branch -r -d "tags/$tagname"; done

```

该命令将原本以 tag/ 开头的远程分支的索引变成真正的（轻巧的）标签。

接下来，把 refs/remotes 下面剩下的索引变成本地分支：

```shell

$ git for-each-ref refs/remotes | cut -d / -f 3- | grep -v @ | while read branchname; do git branch "$branchname" "refs/remotes/$branchname"; git branch -r -d "$branchname"; done

```

现在所有的旧分支都变成真正的 Git 分支，所有的旧标签也变成真正的 Git 标签。最后一项工作就是把新建的 Git 服务器添加为远程服务器并且向它推送。下面是新增远程服务器的例子：

```shell

$ git remote add origin git@my-git-server:myrepository.git

```

为了让所有的分支和标签都得到上传，我们使用这条命令：

```shell

$ git push origin --all
$ git push origin --tags

```

所有的分支和标签现在都应该整齐干净的躺在新的 Git 服务器里了。
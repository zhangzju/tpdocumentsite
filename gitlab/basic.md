## 安装 Git

在你开始使用 Git 前，需要将它安装在你的计算机上。 即便已经安装，最好将它升级到最新的版本。 你可以通过软件包或者其它安装程序来安装，或者下载源码编译安装。
如果你想在 Linux 上用二进制安装程序来安装 Git，可以使用发行版包含的基础软件包管理工具来安装。 如果以 Fedora 上为例，你可以使用 yum：
```shell
sudo yum install git
```
如果你在基于 Debian 的发行版上，请尝试用 apt-get：
```shell
sudo apt-get install git
```
要了解更多选择，Git 官方网站上有在各种 Unix 风格的系统上安装步骤，网址为 http://git-scm.com/download/linux。

在 Windows 上安装,也有几种安装方法。 官方版本可以在 Git 官方网站下载。 打开 http://git-scm.com/download/win，下载会自动开始。 要注意这是一个名为 Git for Windows的项目（也叫做 msysGit），和 Git 是分别独立的项目；更多信息请访问 http://msysgit.github.io/。

另一个简单的方法是安装 GitHub for Windows。 该安装程序包含图形化和命令行版本的 Git。 它也能支持 Powershell，提供了稳定的凭证缓存和健全的 CRLF 设置。 稍后我们会对这方面有更多了解，现在只要一句话就够了，这些都是你所需要的。 你可以在 GitHub for Windows 网站下载，网址为 http://windows.github.com。

有人觉得从源码安装 Git 更实用，因为你能得到最新的版本。 二进制安装程序倾向于有一些滞后，当然近几年 Git 已经成熟，这个差异不再显著。

如果你想从源码安装 Git，需要安装 Git 依赖的库：curl、zlib、openssl、expat，还有libiconv。 如果你的系统上有 yum （如 Fedora）或者 apt-get（如基于 Debian 的系统），可以使用以下命令之一来安装最小化的依赖包来编译和安装 Git 的二进制版：
```shell
  $ sudo yum install curl-devel expat-devel gettext-devel \
    openssl-devel zlib-devel
  $ sudo apt-get install libcurl4-gnutls-dev libexpat1-dev gettext \
    libz-dev libssl-dev
```
为了能够添加更多格式的文档（如 doc, html, info），你需要安装以下的依赖包：
```shell
  $ sudo yum install asciidoc xmlto docbook2x
  $ sudo apt-get install asciidoc xmlto docbook2x
```
当你安装好所有的必要依赖，你可以继续从几个地方来取得最新发布版本的 tar 包。 你可以从 Kernel.org 网站获取，网址为 https://www.kernel.org/pub/software/scm/git，或从 GitHub 网站上的镜像来获得，网址为 https://github.com/git/git/releases。 通常在 GitHub 上的是最新版本，但 kernel.org 上包含有文件下载签名，如果你想验证下载正确性的话会用到。

接着，编译并安装：
```shell
  $ tar -zxf git-2.0.0.tar.gz
  $ cd git-2.0.0
  $ make configure
  $ ./configure --prefix=/usr
  $ make all doc info
  $ sudo make install install-doc install-html install-info

```
完成后，你可以使用 Git 来获取 Git 的升级：
```shell
  $ git clone git://git.kernel.org/pub/scm/git/git.git
```
## Feature branch

在gitlab的workflow中，所有的分支都可以划分为受保护的分支和不受保护的分支。对于受保护的分支，其权限是有所管控的，开发人员可以对这个分支提出merge request，对于不受保护的分支，开发人员可以根据自己的续修对分支进行任意的修改，以完成开发的需求。
在整体开发过程中，由于各个开发人员都是单独负责一部分的功能，因此，这部分不受保护的分支被称为Feature branch，而受保护的分支一般是作为特性开发完成，发布时使用到的分支，根据作用的不同被称为Master branch， Release branch等。

对于一般的开发人员来说，所有的工作只需要在Feature branch上完成即可。

## 项目开发准备操作

一个项目在进行开发的时候，需要明确两个问题：
1. 划分功能模块，为耦合度不高的模块划分单独的feature branch 
2. 为每个分支指定开发人员

在开发人员和分支的划分都到位之后，即可开始开发工作。

## 开发流程：在Feature branch上的操作

一般开发人员需要进行一项功能的定制或者开发时，一般的流程如下：
1. Clone project:
```shell
 git clone git@example.com:project-name.git
```
项目开发一般是基于一个已经在维护中的项目，一般一个项目就意味着一个需求，例如VR500项目和VR500的某个定制单项目，尽管他们之间代码非常接近，甚至仅有配置文件的区别，但是因为最后需要发布的软件是不一样的，所有应当属于不同的项目。

2. Create branch with your feature:
```shell
 git checkout -b $feature_name
```
分支可以由管理人员来创建，这样有利于遵循统一的命名规范。各个分支之间需要主要解耦，防止代码之间出现conflict。
3. Write code. Commit changes:

```shell
 git commit -am "My feature is ready"
```
一般来说，feature branch不属于protected branch，因此开发人员可以随意Push自己的代码，但是对于合理的开发流程，应该是在修改之后增加commit，在确定修改不太有可能反复的时候再Push，减少管理人员的负担。


4. Push your branch to GitLab:
```shell
 git push origin $feature_name
```

5. Review your code on commits page.
在开发人员的代码Push之后，管理人员就可以对代码进行Review或者指派人员进行Review操作。

6. Create a merge request.
在完成代码的Review之后，就可以发出一个合并的请求，和并请求有时候会带来和原有代码的冲突，这时候需要进行stash和stash pop的操作，避免出现文件内的HEAD指针问题，当然，出现了也没有问题，逐个文件进行解决即可。

7. Your team lead will review the code & merge it to the main branch.
管理人员进行合并操作之后，即可对产品进行发布。

## 后续操作

项目开发完成并且发布之后，这个项目一般都会进入maintain状态，尽管没有开发需求，但是日常使用，测试会有相应的反馈，并且需要有bug的修复，在修复bug的过程中，每一个release版本都会有一个hotfix分支，所有的bug修复均在hotfix分支上实现，hotfix分支的
意义在于即使产品并未发布，依然能够有较为完善的小版本不断释出，是产品处于维护的状态。此外，还有关于特性增删修改的issue会在产品的issue tracker中进行讨论。主要的善后工作如下：

1. 在hotfix分支上解决严重的Bug
2. 在issue tracker中解决使用问题
3. 在wiki中队产品的迭代changelog进行补充
4. 在项目完全结束或者废弃的时候，为项目打上deprecated标签
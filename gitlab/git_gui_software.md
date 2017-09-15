## git图形界面工具概述

图形界面工具有时候能够帮助新手快速入门并了解git的整个流程，简化了许多重复的操作，提高开发的效率。

目前在社区中git图形界面工具的种类非常众多，这里简介四种不同类型的工具，大家可以根据需要选择使用。

2017.09.14更新：经过一段时间的推广，git图形界面扩展工具sourcetree得到大家的认可，特此加入推荐套餐。另外特别推荐命令行git增强工具Oh My ZSH，命令行用户不可错过的神器。

**注意：本文提到的共享路径为**
```shell
\\exfile.tp-link.net\运营商事业部\运营商研发部\Software\Common\4-开发软件\持续集成\git
```
## SourceTree

### 安装以及破解

这个软件的安装包已经放置在前文提到的共享路径之中。

这个软件第一次安装的时候需要登入atlassian账户进行验证，由于内网无法登陆，所以我们也想到了应对的策略，伪造了数据。在共享路径中，有一个名为account.json的文件，大家遵循以下步骤操作：

* 点击安装包安装，出现需要输入账户的时候退出安装
* 将account.json文件复制，放到自己用户文件夹的AppData\Local\Atlassian\SourceTree路径下，注意，这个文件夹默认是隐藏的，大家需要先使其可见
* 重新点击安装包，这时出现点选Ｍercurial的界面，直接选择不使用，即可进入主界面

### 功能特性

这款软件速度较快，第一次夹在samba服务器上的文件会有点慢，需要耐心等待，但是之后的每一次查看都会非常迅速。

使用界面如下图：

![sourcetree](imgs/git_gui_tools/st.png)

## Oh My ZSH

### 安装

Oh-My-Zsh 是 Zsh　的扩展.　通过对zsh的丰富定制，让经常使用git和命令行的开发人员事半功倍。

安装Oh-My-Zsh，首先你需要安装Zsh，直接采用apt进行安装即可：

```shell
sudo apt install zsh -y
```
然后能够使用研发处外网权限白名单的同事们可以通过下面的脚本来安装：

```shell
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```
如果没有外网权限，只需要让安装好的同事们把用户主目录下的.zshrc复制给你即可。


### 主要配置

Oh-My-Zsh有着丰富的主题，运行的界面如下图：
![af-magit](imgs/git_gui_tools/af-magic.jpg)

还有如下比较出色的主题：

![af-magit](imgs/git_gui_tools/eastwood.jpg)
![af-magit](imgs/git_gui_tools/nebirhos.jpg)

然而，外观并不是Oh-My-Zsh能够脱颖而出的最重要的原因，Oh-My-Zsh内置了几乎所有你能用到的git命令的简写，例如我们常用的命令：

```shell
git diff --cached
```
在这里只需要：

```shell
gdca
```
更多的详情要参考官方的文档。

## Tortoise Git 

在SVN时代，大家都很习惯使用Ｔortoise　SVN这个工具来进行查看log等等常规的操作，那么你完全可以无缝切换到这个工具来。

### 安装

这个软件只有Windows版本：

1. 在共享路径中可以获取这个软件的安装包，是Ｗindows的可执行安装包，使用管理员权限一直点击即可安装。
2. 在同一个目录下还有这个软件的中文语言包，在安装好软件之后，直接点击即可安装汉化包。

### 功能特性

TortoiseGit是一款大而全的软件，使用时的界面如下图：

![TortoiseGit](imgs/git_gui_tools/tortoisegit.PNG)

我们可以使用本软件来进行仓库中的历史信息的浏览，差异的比对，但是，TortoiseGit的强大功能还远远不止这些，我们可以看一下本软件的右键菜单：

![RightMenu](imgs/git_gui_tools/rightmenu.PNG)

我们可以发现他几乎覆盖了我们日常所需要的所有功能，大家可以利用这个软件进行本地操作，只需要鼠标选取即可。

这款软件能够包办所有操作，但是依然还是要明白git的整个流程，才能更高效的使用大而全的软件。

## Visual Studio Code

这款软件是又微软开发的一款轻量级代码编辑器，具有全平台，丰富的插件支持，丰富的主题以及外观配置，支持热调试等强大的功能。



### 安装

这个软件提供了Ｗindows版本和Ubuntu的deb版本，安装文件在专属的共享路径中。

在windows平台上，获得安装包之后直接点击即可完成安装，git是编辑器自带的插件。

在Ubuntu平台中，获取安装文件到/tmp路径下，执行下列命令：

```shell
sudo dpkg -i vscode.deb #根据具体的版本切换deb包的名字
```
安装完成之后就可以在software中打开，或者在命令行中直接输入vscode即可打开编辑器。

### 使用简介

与Ｖisual studio不同的是，这个软件并不是一个集成开发工具，只是一个代码编辑器，界面美观，而且支持丰富的扩展，是我使用过的编辑器中出类拔萃的。

具体的使用界面如下图：

![vscode](imgs/git_gui_tools/vscode.png)

可以看到，在专门为版本管理系统设置的快捷左侧图表点击之后，会进入git操作的侧边栏，点击菜单即可进行许多常规的操作，对于vscode来说，他支持我们在日常工作中的所有本地操作，能够满足所有的本地开发的要求，而且更棒的是，他能够随时使用快捷键"ctrl+shift+p"打开命令行，能够实现图形界面和命令行的配合操作。

## gitk

gitk是git工具自带的简易的图形界面工具。

### 安装

在你安装完git之后，gitk工具就可以使用了。

### 使用简介

gitk相比于其它类型的图形界面操作工具，主要的目标有所差异，gitk的目标时弥补命令行操作中的短板，即本地仓库与远程仓库的差异，本地仓库不同提交之间的差异的可视化。

运行界面如下图：

![gitk](imgs/git_gui_tools/gitk.png)

可以发现gitk的界面非常的简单，可以查看不同分支，不同作者的提交记录，这一点在命令行中需要多步操作，在这项工具中会很方便。如果你更喜欢在命令行中做操作，那么gitk会是一个不错的选择。

## Gitkraken

这是一款强大的，美观的，专注于对比不同版本之间差异的软件。

### 安装

这个软件提供了Ｗindows版本和Ubuntu的deb版本，安装文件在专属的共享路径中。

在windows平台上，获得安装包之后直接点击即可完成安装，git是编辑器自带的插件。

在Ubuntu平台中，获取安装文件到/tmp路径下，执行下列命令：

```shell
sudo dpkg -i gitkraken.deb #根据具体的版本切换deb包的名字
```
安装完成之后就可以在software中打开.

### 功能特性

这款工具可以说是gitk的升级版本，在很多方面，尤其是在真是分支以及具体文件内容的变化中有了很大的增强，而且有非常出色的界面。

运行时的界面如下图：

![gitkraken](imgs/git_gui_tools/gitkraken.png)

图中可以清晰的看到，除了进行本地操作之外，gitkraken采用流水先方式展现git的变化，同时采用不同颜色来区分代码之间的差异。

这款软件会使用本地缓存，因此第一次夹在某个仓库会较慢，之后的使用就会很快。
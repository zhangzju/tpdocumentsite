## 安装 Git

在你开始使用 Git 前，需要将它安装在你的计算机上。 即便已经安装，最好将它升级到最新的版本。 你可以通过软件包或者其它安装程序来安装，或者下载源码编译安装。

### Unix类系统

如果你想在 Linux 上用二进制安装程序来安装 Git，可以使用发行版包含的基础软件包管理工具来安装。 如果以 Fedora 上为例，你可以使用 yum：
```shell
sudo yum install git
```
如果你在基于 Debian 的发行版上，请尝试用 apt-get：
```shell
sudo apt-get install git
```
要了解更多选择，Git 官方网站上有在各种 Unix 风格的系统上安装步骤，网址为 http://git-scm.com/download/linux。

### Windows系统

我们在共享的专用路径中提供了git的下载，只需要不断点击下一步即可安装。目前暂时无法保证及时和最新版本的git保持一致，不过由于现在git大多数是基于安全的更新，因此提供的安装版足够使用。

在 Windows 上安装,也有几种安装方法。 官方版本可以在 Git 官方网站下载。 打开 http://git-scm.com/download/win，下载会自动开始。 要注意这是一个名为 Git for Windows的项目（也叫做 msysGit），和 Git 是分别独立的项目；更多信息请访问 http://msysgit.github.io/。

另一个简单的方法是安装 GitHub for Windows。 该安装程序包含图形化和命令行版本的 Git。 它也能支持 Powershell，提供了稳定的凭证缓存和健全的 CRLF 设置。 稍后我们会对这方面有更多了解，现在只要一句话就够了，这些都是你所需要的。 你可以在 GitHub for Windows 网站下载，网址为 http://windows.github.com。


### 源码安装

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

## 初次运行 Git 前的配置

### git配置文件概述

Git 自带一个 git config 的工具来帮助设置控制 Git 外观和行为的配置变量。 这些变量存储在三个不同的位置：
1. /etc/gitconfig 文件: 包含系统上每一个用户及他们仓库的通用配置。 如果使用带有 --system 选项的 git config 时，它会从此文件读写配置变量。

2. ~/.gitconfig 或 ~/.config/git/config 文件：只针对当前用户。 可以传递 --global 选项让 Git 读写此文件。

3. 当前使用仓库的 Git 目录中的 config 文件（就是 .git/config）：针对该仓库。

每一个级别覆盖上一级别的配置，所以 .git/config 的配置变量会覆盖 /etc/gitconfig 中的配置变量。

在 Windows 系统中，Git 会查找 $HOME 目录下（一般情况下是 C:\Users\$USER）的 .gitconfig 文件。 Git 同样也会寻找 /etc/gitconfig 文件，但只限于 MSys 的根目录下，即安装 Git 时所选的目标位置。

##＃ 配置用户信息

在初次使用git的时候，需要进行一些简单的配置，其中用户信息是必须要进行的，当然，这些用户信息虽然会包含到之后的每一次commit中，但是你随时可以更改他。配置方法如下：

```shell
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```
再次强调，如果使用了 --global 选项，那么该命令只需要运行一次，因为之后无论你在该系统上做任何事情， Git 都会使用那些信息。 当你想针对特定项目使用不同的用户名称与邮件地址时，可以在那个项目目录下运行没有 --global 选项的命令来配置。

### 适用于内网gitlab服务器的配置

我们在内网使用git的时候，需要有一些专门的配置，解决一些大家常见的问题。具体的配置如下:

```shell
git config --global core.filemode false #设置默认不跟踪文件权限和属性的变化
git config --global http.sslVerify false　#设置默认不启用SSL层进行
git config --global push.default current　#默认推送策略更改为只推送本分支的更改
```

需要说明的是，这些设置仅适合在内网中进行，因为这些操作实际上并不安全，大家在家里开发的时候就不要这么设置了。



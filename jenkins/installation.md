## jenkins安装

jenkins易于安装，作为一个java应用程序，他可以作为一个独立的应用程序，也可以部署在传统的Java应用服务器，例如Tomcat或者JBoss上。

最新版本的Jenkins需要至少Java5以上的平台支持，Jenkins有多种形式的分发，包括Java Web应用程式_WAR__安装包，源码编译安装的tar.gz包以及适合各个发行版的Rpm包，deb包等。

首先准备好安装包，由于需要安装在Ubuntu Server上，我们准备的是deb版本，首先使用dpkg安装jenkins：

```shell
 sudo dpkg -i jenkins-xxx.deb
```
如果有部分以来没有能够安装完成的话，会提示缺少运行环境的错误，然后通过apt来解决：

```shell
 sudo apt-get install -f
```
这样就能够将所有的依赖全部补充。

jenkins安装完成后默认在8080端口开放web访问。

## jenkins主目录

在我们安装jenkins之前，需要知道jenkins的一些工作原理。jenkins会将所有的重要数据存放在一个专用的、隔离的，称为Jenkins主目录的路径下，在这里，Jenkins存储关于构建服务器的配置信息，配置作业、构建产物和其他有用的信息，也包括各种已安装的插件。

因此，这个路径会耗费大量的欧诺关键，默认情况下，Jenkins主目录会是在用户目录下的 __.jenkins__ 路径，如果是通过deb包安装或者包管理器安装的Jenkins，则主目录会是在 __/var/lib/jenkins__ 下，这个路径可以在web界面的系统设置中查看。

Jenkins主目录下包含了Jenkins服务器配置的细节，核心配置是存储在 **config.xml** 中，制定工具的配置存储在其他适当命名的XML文件中，其他的一些路径也有着不同的用途：

* jobs路径包含Jenkins管理的构建作业的配置细节，以及这些构建所输出的产物和数据
* plugin路径包含了已经安装的插件
* updates路径存放关于插件的更新
* userContent路径存放一些为Jenkins服务器定制化的一些内容
* users路径存放本地用户账户信息数据库
* war包含了标准的web应用扩展程序

关于Jnekins 的备份，只需要备份这一个路径下的文件即可，因为Jenkins是向下兼容的，所以只要下一次安装时安装最新的版本就可以还保证还原到之前的构建状态。


## 使用docker部署jenkins

jenkins还提供了基于官方最新版本的docker image用于快速部署的环境。对于jenkins来说最为关键的部分是jenkins的主目录，我们在更换版本时也只需要关注这个目录，因此，我们在部署docker的时候，也不用考虑其他。

在docker的版本迭代中，我们只需要保留 **jenkins_home** 在本地，然后每次运行jenkins的时候就将 **jenkins_home** 挂载到容器中成为数据卷，就可以方便二快捷的部署jenkins应用。

使用docker不是jenkins还有一个要点，jenkins默认的8080端口会被很多程序占用，所以在映射端口的时候最好指定一个比较大的端口，或试着用-P参数来随机分配端口，避免因为端口冲突而导致的异常。具体的指令如下：

```shell
sudo docker run -d --name myjenkins -p 45678:8080 -v /var/jenkins_home:/var/jenkins_home jenkins
```


## Jenkins文档

Jenkins是一个有Java语言编写的持续集成工具。易于维护和使用，有开源社区的强大支持们能够极大地提高产品持续交付和版本迭代的能力。


## 相关导航

### 内网

- [Jenkins服务器](http://spjenkins.rd.tp-link.net)

### Internet

- [Jenkins官方网站](http://www.jenkins.org)

## 持续集成相关文档

### 针对管理人员的Jenkins操作指南

- [pipeline基础语法](/doc/#/jenkins/pipeline) *
- [email-ext以及其他插件的使用](/doc/#/jenkins/use_plugins) *
- [daily build的设置以及中间产物的输出](/doc/#/jenkins_daily_build) *

### 持续集成化的项目

- [RE200(SP)v2](/doc/#/jenkins/re200spv2)
- [GR1600v](/doc/#/jenkins/gr1600v)
- [VR1200](/doc/#/jenkins/vr1200) 
- [C20(SP)v1](doc/#/jenkins/c20) *

### 针对运维人员的Ｊenkins操作指南

- [Jnekins的安装与部署](/doc/#/jenkins/installation)
- [邮件通知设定](/doc/#/jenkins/configuration)
- [使用thinBackup进行备份](/doc/#/jenkins/thinbackup) *
- [插件安装以及性能监控](/doc/#/jenkins/plugin_install) *

## Q&A

### git-svn迁移及jenkins部署问题

Ｑ:如何进行git-svn对仓库进行转换，这部分遇到了很多问题，大致包括：
1. git-svn工具在不同操作系统中的支持不同
2. git工具本身版本不同导致默认配置的差异

Ａ：经过测试发现，采用Roy发布的ubuntu14.04_v3.2镜像之后，可以规避这些问题，详见文档　[使用docker进行快速迁移](/doc/#/gitlab/docker_git_svn) *

Ｑ：上传代码到gitlab,使用git push等指令的时候出现fatal:server certificate verification failed. 错误？

Ａ：这是由于git的SSL会话层协议策略导致的，解决方法为设置git默认不通过SSL进行传输。
```shell
git config --global http.sslVerify false
```

Ｑ：上传代码之后发现设置错误，路径有问题？

Ａ：由于gitlab上的路径等等的设置可以更改，所以需要操作的是两个步骤：
1. 在gitlab上找管理员进行操作，修改Project的相关信息。
2. 在本地对仓库进行操作，修改仓库的remote分支，使之对应到gitlab上修改之后的地址。

Ｑ：通过docker save导出的镜像在新的操作系统中引入后生成了无主镜像怎么处理(镜像缺少tag和version)?

A: 生成无主镜像的情况在windows下出现过，由于对镜像的最稳妥的标记为Hash值，因此并不影响具体的操作，但是无主镜像会被一些清理命令所删除，目前需要在导入之后对镜像打上新的tag，不过在使用时建议还是采用Hash值来区分每个镜像。

Ｑ：单独构建可以成功的工程在daily build中会失败，原因是：

```shell
checking for suffix of executables... 
checking whether we are cross compiling... configure: error: in `/home/bba/git-src/platform_BBA1.5/host_tools/xz-5.2.1':
configure: error: cannot run C compiled programs.
```
这个问题如何解决？

Ａ：经过多次测试和观察发现这个问题和daily build的时间设置有关系，当两个项目在同一时间进行build的时候，会出现上述问题，需要解决的话，就需要合理安排自动编译的时间。

### Docker编译最常见问题

Ｑ：在Ｗindows下使用docker之后，载入镜像时会出现
```shell
Error processing tar file(exit status 1): link /opt/trendchip/mips-linux-uclibc-4.36-v2/usr/mips-unknown-linux-uclibc/sysroot/usr/share/terminfo/2/2621/opt/trendchip/mips-linux-uclibc-4.36-v2/usr/mips-unknown-linux-uclibc/sysroot/usr/share/terminfo/2/2621-wl:no such file or directory
```
或者类似的问题？

Ａ：这个问题只会在Ｗindows下出现，会出现在较新版本的docker中，具体原因未知，目前在boot2docker中有了解决的方案，即替换最新版本的boot2docker.iso的镜像为Roy提供的位于共享中的镜像，或者使用Ｕbuntu下的docker.

Ｑ：在使用docker进行编译的时候，遇到类似“某个模块编译有问题，删除之后重新update就编译通过了”这样的问题？

Ａ：经过多次解决发现，这个问题属于时间戳导致make的顺序以及是否模块是否执行的缘故，一般采用脚本将相关的模块的代码全都进行touch操作即可，脚本模板如下：

```shell

echo "**************** Auto Make Timestamp touch ******************"
filelist="Makefile.in Makefile.flat Makefile aclocal.m4 conf.in \
configure.in *config?h.in *config.in configure config.status config.h"
for i in $filelist
do
echo " touch \"$i\""
find . -name "$i" | xargs -i{} touch -c {}
done
echo "Done"
echo

```

经过测试，目前RE200已经解决。



Ｑ：如果在Ｗindows中使用docker,由于Ｗindows系统禁止在文件或路径中带有冒号，因此有些中间生成产物是无法下载的？

Ａ：由于系统限制，所以只能采用其他方式解决，目前最好的方式时，使用新建一个数据卷VOLUME的方式来解决，这样既不影响原有的docker的编译，又能保证代码和环境的解耦，具体操作见文档　[使用docker进行快速迁移](/doc/#/gitlab/docker_git_svn) *

Ｑ：在编译过程中，某些项目需要安装类似ppp的依赖，在安装时需要进行很多交互操作，如何在自动编译的时候规避这种情况？

Ａ：交互操作目前暂时还没有很好的解决办法，但是出于减少耦合的需求，用于编译环境的镜像不应该在编译过程中进行安装依赖的操作，之后出现这一类的问题，需要对docker镜像进行更新，发布安装好依赖的镜像用于编译。

Ｑ：在原有的镜像生成的容器中进行编译，出现需要的编译链与本机型不匹配怎么办？

Ａ：同上一个问题，这些属于环境的适配，一般的操作是使用软连接，通过编译之后就对镜像再一次进行打包，更新环境。

Ｑ：在部分机型的fs_build中，会出现需要先进行configure来生成Ｍakefile的情况，如何处理？

Ａ：通过脚本先删除掉原先的Ｍakefile,然后执行configure,即可解决这个问题。






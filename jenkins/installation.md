## jenkins安装

jenkins易于安装，作为一个java应用程序，他可以作为一个独立的应用程序，也可以部署在传统的Java应用服务器，例如Tomcat或者JBoss上。

安装jenkins有两种方式，第一种是添加jenkins官方网站的pkg站点作为源，然后使用包管理器进行安装，第二种是使用官方释出的安装包进行安装，由于网络原因，第一种方式经常会遇到问题，因此这里采取安装包的方式进行安装。

首先准备好安装包，jenkins支持多国语言，由于需要安装在Ubuntu Server上，我们准备的是deb版本，首先使用dpkg安装jenkins：

```shell
 sudo dpkg -i jenkins-xxx.deb
```
如果有部分以来没有能够安装完成的话，会提示缺少运行环境的错误，然后通过apt来解决：

```shell
 sudo apt-get install -f
```
这样就能够将所有的依赖全部补充。

jenkins安装包默认的端口是8080，对于测试使用来说，这个端口可能被占据，如果访问8080端口发现没有响应，可以查看jenkins的运行时日志，路径是：
```shell
/var/log/jenkins/jenkins.log
```
同时，可以在配置文件中调整jenkins的启动参数，切换端口，配置文件的路径是：
```shell
 /etc/default/jenkins
```

## 使用docker部署jenkins

## jenkins安装插件
## gitlab文档

gitlab是开源社区最大的分布式版本控制系统git的CMS实现，基于Ruby on Rails，功能强大，提供工作流支持。


## 相关导航

- [Spcodes服务器](http://spcodes.rd.tp-link.net)

## 相关文档

### 针对开发人员的git基本文档

- [git核心概念](/doc/#/gitlab/git-core)
- [gitlab基础操作](/doc/#/gitlab/basic)
- [gitlab-ssh设置](/doc/#/gitlab/gitlab-ssh)
- [常见git图形界面工具使用](/doc/#/gitlab/git_gui_software) *

### 针对项目负责人的git迁移相关

- [从SVN迁移到git](/doc/#/gitlab/gitlab-svn)
- [How to work with gitlab](/doc/#/gitlab/SPBU_SOP_How_to_work_with_gitlab)
- [使用docker进行快速迁移](/doc/#/gitlab/docker_git_svn) *

### 针对运维人员的gitlab相关

- [gitlab邮箱设置](/doc/#/gitlab/gitlab-smtp)
- [gitlab权限控制](/doc/#/gitlab/gitlab-control)
- [gitlab关键数据的备份](/doc/#/gitlab/gitlab_backup) *

### 针对管理人员的gitlab操作指南

- [gitflow工作流](/doc/#/gitlab/gitflow)
- [针对基准项目开发新的功能](/doc/#/gitlab/new_feature) *
- [针对定制单开发新的分支](/doc/#/gitlab/new_specific) *

## Q&A

### push.default设置

Ｑ：在直接使用git push进行代码提交时，出现以下问题提交被拒绝，远程分支却并不是protected分支？(王洋)

push.default是设定默认的push规格的全局参数，如果git push命令没有明确指定引用规格(refspec),也就是没有指定推送的源分支和目标分支，那么git会采用push.default定义的动作。不同的值适用于不同的工作流程模式：

1. nothing:不推送任何东西并有错误提示，除非明确指定分支引用规格。强制使用分支引用规格来避免可能潜在的错误

2. current:推送当前分支到接收端名字相同的分支。

3. upstream:推送当前分支到上游@{upstream}。这个模式只适用于推送到与拉取数据相同的仓库，比如中央工作仓库流
程模式。

4. simple:在中央仓库工作流程模式下，拒绝推送到上游与本地分支名字不同的分支。也就是只有本地分支名和上游分支名字一致才可以推送，就算是推送到不是拉取数据的远程仓库，只要名字相同也是可以的。在GIT 2.0中，simple将会是push.default的默认值。simple只会推送本地当前分支。

5. matching:推送本地仓库和远程仓库所有名字相同的分支。这是git当前版本的缺省值。

一般将这个配置改为current即可。

```shell

git config --global push.default current

```

### core.filemode设置

Ｑ：在clone代码后未作更改，但是修改了文件权限，导致所有的文件都被git status识别为已经修改？（冼健涛）

这是因为高版本的git会默认观察文件的模式的变更，需要通过全局设置将这种观察关闭。

```shell

git config --global core.filemode false

```

### git gc清理缓存文件

Ｑ：在进行一个内容较多的repo的fetch或者clone操作时，会出现“Auto packing the repository for optimum performance. You may also　run "git gc" manually. See "git help gc" for more information.”类似的错误如何处理？

Git 往磁盘保存对象时默认使用的格式叫松散对象 (loose object) 格式。Git 时不时地将这些对象打包至一个叫 packfile 的二进制文件以节省空间并提高效率。当仓库中有太多的松散对象则就会提示你运行 ' git gc '。

由于虚拟机性能等原因，出现这样的情况的时候，需要运行：

```shell

git gc

```

### https方式的push或者clone出现fatal错误

Ｑ：当使用https的方式进行与服务器的代码传输与提交操作时，由于git的默认ssl规则会出现

```shell
server certificate verification failed. CAfile: /etc/ssl/certs/ca-certificates.crt CRLfile: none

```
的问题，通常有两种处理办法，但是处于减少环境干扰的方式考虑，可以设定git的默认传输规则，忽略ssl的认证，在内网的操作中可以这么做，但是如果涉及到外网，这样做会有风险。

```shell

git config --global http.sslVerify false

```








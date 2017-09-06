## docker文档
docker是一种轻量级的新型的容器技术，在快速部署，环境迁移以及集群部署方面具有巨大的潜力，基于Go语言，是目前云计算领域最为火热的技术。

## 相关导航

- [Docker shipyard](http://172.29.77.114:8080)

## 相关文档

### 针对开发人员docker基础文档
- [docker基本概念](/doc/#/docker/basic_concept)
- [docker环境的部署](/doc/#/docker/windows_deploy)
- [docker基本使用](/doc/#/docker/basic_work)
- [使用数据卷容器解决数据格式问题](/doc/#/docker_volume)

### 针对项目负责人的docker维护文档

- [基于ubuntu14.04_mtk_v3.2构建编译镜像](/doc/#/docker/make_image_based_on_mtkv32) *
- [Ｄockerfile基础](/doc/#/docker/dockerfile_basic) *
- [基于alpine构建应对简单buxybox场景的docker镜像](/doc/#/docker/alpine_busybox) *


### docker技巧与进阶
- [shipyard管理docker节点](/doc/#/docker/shipyard)
- [docker中残余的清理](/doc/#/docker/template_delete)
- [docker网络配置](/doc/#/docker/docker_network) *
- [docker三剑客swarm,compose,machine](/doc/#/docker/docker_swarm) *

## Q&A

### client与server版本不一致

Q: Error response from daemon: client is newer than server (client API version: 1.24, server API version: 1.22)

Ａ：这个问题通常是在更新了系统的软件之后产生的，由于docker的daemon进程并没有更新，因此会出现版本差异无法通信的问题，此时需要执行：

```shell

sudo systemctl restart docker
```


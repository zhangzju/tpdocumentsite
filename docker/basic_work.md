# 常用指令

__注意：所有的docker指令均需要在root用户状态下才能正常运行__
## docker pull

从 Docker Registry 获取镜像的命令是 docker pull。其命令格式为：
```shell
docker pull [选项] [Docker Registry地址]<仓库名>:<标签>
```
* Docker Registry地址：地址的格式一般是 <域名/IP>[:端口号]。默认地址是 Docker Hub。
* 仓库名：仓库名是两段式名称，即 <用户名>/<软件名>。对于 Docker Hub，如果不给出用户名，则默认为 library，也就是官方镜像。

## docker run

创建一个新的容器并运行一个命令，
当利用 docker run 来创建容器时，Docker 在后台运行的标准操作包括：

* 检查本地是否存在指定的镜像，不存在就从公有仓库下载
* 利用镜像创建并启动一个容器
* 分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
* 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
* 从地址池配置一个 ip 地址给容器
* 执行用户指定的应用程序
* 执行完毕后容器被终止

是最常见的docker命令，格式为：
```shell
docker run [OPTIONS] IMAGE[:TAG] [COMMAND] [ARG...]
```
简单的介绍一下常用的参数：
1. -a STDIN: 指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项；
2. -i: 以交互模式运行容器，通常与 -t 同时使用；
3. -t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用；
4. --name="nginx-lb": 为容器指定一个名称；
5. -d=true或者-d: 容器将会运行在后台模式,此时所有I/O数据只能通过网络资源或者共享卷组来进行交互,因为容器不再监听你执行docker run的这个终端命令行窗口;
6. -p或者-P:那么容器会开放部分端口到主机，只要对方可以连接到主机，就可以连接到容器内部。当使用-P时，Docker会在主机中随机从49153 和65535之间查找一个未被占用的端口绑定到容器。你可以使用docker port来查找这个随机绑定端口;
7. -v=[]: 创建一个数据卷并挂载到运行的container上，格式是: 
```shell
[host-dir]:[container-dir]:[rw|ro].
```

关于我们的使用，一般会是如下的一条命令：
```shell
docker run -it -v /home/bba:/opt/bba --name="vr500" ericchu/vr500
```
## docker  ps

使用docker ps可以查看到目前的容器运行状况，常用的参数有：
* -a：包含所有container，无论是否中止

对于docker ps来说，最大的作用是查看docker container 的ID，以便进行下一步操作。

## docker start && docker stop
可以利用 docker start 命令，直接将一个已经终止的容器启动运行。
```shell
docker start [OPTIONS] CONTAINER [CONTAINER...]
```
这里的CONTAINER可以使用三种方式来选择：
1. 完成的Container ID
2. uuid，及前面一部分ID，足够区分即可
3. name， 即在一开始docker run的时候制定的name

有时候，我们会将一个容器放在后台运行，以便于多次重复进入使用，这时，如果我们需要使用这个容器的某个端口的时候，就需要关闭这个容器，这时候就应该使用docker stop指令：

```shell
docker stop CONTAINER
```
用法类似于start.
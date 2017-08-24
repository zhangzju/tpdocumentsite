# 常用指令

__注意：所有的docker指令均需要在root用户状态下才能正常运行__
## 获取docker　image

### 从镜像仓库获取docker image

从 Docker Registry 获取镜像的命令是 docker pull。其命令格式为：
```shell
docker pull [选项] [Docker Registry地址]<仓库名>:<标签>
```
* Docker Registry地址：地址的格式一般是 <域名/IP>[:端口号]。默认地址是 Docker Hub。
* 仓库名：仓库名是两段式名称，即 <用户名>/<软件名>。对于 Docker Hub，如果不给出用户名，则默认为 library，也就是官方镜像。

### 通过导入tar包获取

在我们的工作中，主要的方式还是通过倒入已经配置好的完整的docker image的镜像包进行镜像的导入。

例如，现在我们有一个名为ubuntu14.04_V3.2.tar 的镜像包，那么需要通过下面的指令进行导入：

```shell

docker load --input ubuntu14.04_V3.2.tar 

```

有的时候，这个命令会出现无法识别的情况，因为不同的系统中存在这文件名数据库的缓存的识别方式的差异，那么可以切换到以下命令：

```shell

docker load < ubuntu14.04_V3.2.tar 

```

一般以上两个命令总有一个是可用的。

### 通过传递容器快照来获取

当我们创建一个docker image的时候，往往是因为这个image在之后的过程中会被反复多次利用，所以进行save的操作，减少重复的工作，但是，很多场景中，我们可能只需要当前的这个容器只会被其他同事使用一两次，这样的情况下，再制作一个镜像是非常不经济的，这时候我们就可以通过传递容器快照的方式来获取容器，传递中的容器也是被打包成为tar格式，例如，现在有名为ubuntu_container.tar的快照，那么可以通过以下命令来声场一个运行中的容器：

```shell

cat ubuntu_container.tar | docker import - ubuntu_container

```

当有需要传递一个容器的快照的时候，则需要如下命令即可：

```shell

docker export <HASH> >  ubuntu_container.tar

```

## 运行docker

### docker run　运行一个全新的docker

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

### docker start 运行一个被exited的docker

在任意一个运行中的docker容器中，我们只需要在命令行中输入

```shell

exit

```

即可退出当前的容器。

当我们退出容器后，容器并没有被删除，而是处于停止运行的状态，此时，需要再次使用之前的docker的话，就需要如下命令：

```shell

docker start <HASH>

```
其中的HASH也可以替换为docker容器的名称和标签的组合。


## docker管理

### docker  ps 查看目前存在的容器

使用docker ps可以查看到目前的容器运行状况，常用的参数有：
* -a：包含所有container，无论是否中止

### docker attach进入运行中的容器的命令行

有时候我们需要在docker容器运行在后台，例如大多数服务都是不需要进入命令行的，但是有时候我们需要对这些运行在后台的docker进行一些操作的时候，就需要将他们连接到命令行，如下：

```shell

docker attach <HASH>

```

如何退出docker attach但是又能够使docker容器继续运行呢？需要使用docker的一个黑科技，快捷键　*CTRL+P CTRL+Q*　可以非常快捷同时不影响运行地退出当前被连接的容器。

需要注意的是，这个命令能够一次性连接多个容器，但是对于命令行来说，他们是同一个进程，所以任何一个容器的阻塞将会导致所有被连接的容器的命令行不可用。


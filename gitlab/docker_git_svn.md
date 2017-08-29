## 使用docker进行svn到git的快速迁移

### 镜像信息

| REPOSITORY       |  TAG            |  IMAGE ID  |
| ------------- |:-------------:| -----:|
| ubuntu14.04      | V3.2  | 0c83c54ed20e  |
| git-svn       |  1.0      |   0c83c54ed20e  |

这两个镜像仅仅是Tag不同，目前存放在文件共享服务器中。

之后所有的关于这个镜像的更新都会新增到本列表中。

### 操作步骤

1. 导入镜像

```shell
docker load < ubuntu14.04_v3.2.tar
```

2. 进入docker,将存放代码的路径挂载到容器中

```shell
docker run -it -v /path/to/code:/opt/bba/git-src 0c8
```

3. 设置container中的基本git信息

```shell
git config --global user.name "zhangwei"
git config --global user.email "zhangwei_w8284@tp-link.com.cn"  #注意要替换成自己的信息
git config --global http.sslVerify false #默认不使用ssl层进行传输，在内网可以使用
```

4. 在gitlab上获取git remote地址，即远程服务器上对应仓库的地址(如果没有，需要让管理人员进行创建)，同时获取SVN地址，设置为环境变量：

```shell

export SVNURL=http://sohoiipfpublic.rd.tp-link.net/svn/ISP/Tender/Platform/Vocalno_BBA_2_0　#注意替换成自己的仓库信息
export GITURL=https://spcodes.rd.tp-link.net/project/TestProject.git

```

5. 将所有的SVN的历史提交信息保存为本地文件

```shell

svn log "$SVNURL" -q | awk -F '|' '/^r/ {sub("^ ", "", $2); sub(" $", "", $2); print $2" = "$2" <"$2">"}' | sort -u > ./authors-transform.txt

```


6. 将SVN上的代码下载到数据卷文件夹的临时文件夹中

```shell

git svn clone "$SVNURL" --no-metadata --preserve-empty-dirs -A ./authors-transform.txt --no-minimize-url ./temp

```

7. 设置转换完成之后的远程仓库信息

```shell

cd temp
git remote add origin "$GITURL"

```

8. 将代码PUSH到gitlab远程服务器

```shell

git push -u origin --all

```

### 完成后的验证操作

完成上述过程之后，已经完成了到整个gitlab的迁移，此时已经可以docker容器。

进入gitlab相关的界面查看信息是否完全，以及在本地执行：

```shell
git pull
git status

```
此时本地的仓库与远程就一致了。
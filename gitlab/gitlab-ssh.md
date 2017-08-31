## 本地与远程交互的方式

作为一个分布式的版本控制系统，git支持两种本地仓库与远程仓库交互的方式：

1. 利用HTTPS协议，这种方式可以在任何地方进行和远程仓库的交互，每一次交互，在需要进行鉴权的时候都需要输入用户名和密码。
2. 利用SSH协议，这种方式需要额外的配置，在需要鉴权的场合，没有配置的工作环境是无法进行操作的。在配置好之后，可以不必每次输入用户名与密码。


配置SSH的信任环境，包括在本地生成一组密钥，以及将公钥配置到服务器上两个部分。

##　生成密钥

### Ubuntu及其他类unix系统中进行配置

SSH的密钥针对每一个系统用户都不同，请注意将用户切换到自己常用的那个用户来进行以下的操作。

密钥的存放路径为　~/.ssh/ ,即用户的主目录下的SSH配置文件目录。如果你是第一次生成这样的配置，那么会是一组 id_rsa.pub 和 id_rsa　文件，其中前者是公钥文件，后者是私钥文件。

在生成之间，你需要先检查一下你的相关路径中是否具有这两个文件，公钥文件是否为空。如果你已经具有这两个文件而且公钥有一组类似如下的内容的话，那么你可以直接使用现有的公钥进行配置：

```shell
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQC34VEC1SC1R44fLaDvwZEyzQ2uy2oLyIKEoyC5d6VVEvf03LenVfHWAGOdUV9438mBcIyWZKDw3PyK/Mj+caupPYUorO0+l7IUapfW84tUoXrgQJ3+gLO/e5xpJfFKgCDR6CsF3cNjyyv7XAYGPn2O4uyNj+d5FAJOrLVc4+Lqe1336L3SdJIRN37/zV5mp3hdGvttCi8ZOSnzmnldEJXhryMWNjxuoHwQXbVMVJjjqcNttpFCKQ4yfmMGiXda48Mi3FCjADtK6h4eMWWiRss2wpL1LhVe8V4l4xeGONX7oFzG8+x3HxByfyLdK6xYc0xY75kcF0jA9SdZlLYQPlNZu94s2TOzm0XYrHjdJyB29JHvfzUwUeNSVZUcqT55I6rPCtq0yEKloYRZeCpBMjWIzM0/EJ0OpjsPodWFf/U6yKg8JLVCWl/L0dl53lyC2Blr62MmJxWzYKfUWNXIgKRwT/v2IDf9SVnWPqfIcvjSjXOvvO/pl24vqMzfDPa1LIe3iwvne7CD7G3xF/lOwbPdPXz8KMoNtOj54/ddoyCZmPdOqGE5F4cS+bGe6XlQP8CSMvJQlmw0DSJfjyZnJWiSRPuvE8V+TiyREBoV2gEwuJo/yknDYDiA5JE8DZEVbSYq/mpl1J0Qc4t51mFQprmga5puWPtWCuTu+GIiNYCWJw== GitLab
```
如果没有上述的公钥信息，那么需要生成新的公钥：

```shell
ssh-keygen -t rsa -C "GitLab" -b 4096
```
上面这个命令只针对gitlab服务器所需要的限制。在生成密钥的时候，会让你进行一次用户名和密码校验，在这一步你可以将密码留空，那么以后你的每一次与gitlab的交互就可以省去输入密码的过程。

### Windows系统中进行配置

在windows系统有，进行密钥的配置主要有两种方法：

1. 使用 PuttyGen等SSH连接工具来生成，生成的文件同样是位于用户主目录的SSH配置目录中。
2. 在powershell或者git bash中进行，方法和在LINUX中完全一样。


## 在服务器上添加公钥

1. 进入gitlab用户的管理界面，打开Profile settings. 

2. 在弹出来的导航栏中选择SSH keys配置界面.

3. 复制 ~/.ssh/rsa.pub 文件中的内容，一般采用如下命令：

```shell
xclip -sel clip < ~/.ssh/id_rsa.pub #linux操作系统中，需要额外安装xclip

cat ~/.ssh/id_rsa.pub #这样会打印出所有公钥的内容，需要手动在终端中复制，注意不能多复制换行符

cat ~/.ssh/id_rsa.pub | clip　#windows的powershell或者git bash中进行这项操作
```

复制文件中的所有内容，将其黏贴到ssh key配置界面的 'Key' 输入框中:

![git_ssh_pub](imgs/gitlab_ssh_pub.png)



此时，一般会根据你的注释自动生成这个公钥的名字，你也可以对这个名字进行修改，以便于和同邮箱或者同平台的其他密钥进行区分。


## 本地初次交互时存储凭据

在以上的配置完成之后，还不能实现无需密码的交互，之差最后一步：在第一次交互中正确的输入你字啊gitlab登陆时的身份凭据，然后在弹出来的选择是否保存凭据的对话框中选择yes即可。
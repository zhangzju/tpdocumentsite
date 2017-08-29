## 文档站说明

### 站点概述

百分之八十的bug都可以通过阅读文档来解决，剩下百分之二十是网络问题。　　－－－Linux创始人，__Linus Torvalds__

## 快速进入

### - [git以及代码管理](/doc/#/gitlab/index)
### - [docker以及开发环境问题](/doc/#/docker/index)
### - [jenkins及持续集成问题](/doc/#/jenkins/index)
### - [大牛的智慧与小白的感悟](/doc/#/share/index)


## 服务器

### <http://spcodes.rd.tp-link.net>
* gitlab代码管理服务器

###  <http://ispproject.rd.tp-link.net>
* redmine项目管理服务器
* 运营商事业部文档站

###  <http://spjenkins.rd.tp-link.net:8080>
* jenkins构建服务器

###  <http://spdocker.rd.tp-link.net>
* docker镜像托管服务器
* lets chat即时通讯试点　*
* mediawiki部门维基百科　*

## Mardown极速入门 

### 标题

```markdown
 # 这是标题
 ## 这是二级标题
 ### 这是三级标题

 ```

 显示效果：


# 这是标题
## 这是二级标题
### 这是三级标题

### 列表

```markdown
1. First
2. Second
3. Third

OR

* line
* line
* line

```
现实为

1. First
2. Second
3. Third

OR

* line
* line
* line

### 强调
```markdown

**加粗**

*斜体*

~~删除线~~

```
显示为

**加粗**

*斜体*

~~删除线~~


### 代码

英文状态下使用三个"`"来开头，然后添加所需要插入代码的语言：
```markdown

```language
int a = 1;

```



结束的时候另起一行，用三个"`"(反引号)写在行首即可。


language可以替换成所有的编程语言来适应你的代码，显示为：

``` c

int a = 1;

```

### 图片

```markdown

![Alt text](/path/to/img.jpg "Optional title")

```

感叹号开头，中括号包裹着（alt text)，元括号包裹着图片的相对路径。

**大家在添加图片的时候请单独以文章的名字建立文件夹。**

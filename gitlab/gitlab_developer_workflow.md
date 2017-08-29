## 开发人员的workflow概述

对于单一的项目来说，一个开发人员的指的是在gitlab上的角色为developer，针对某个issue建立的分支进行开发，完成后进行提交然后由管理人员进行操作的同事。

开发人员的主要流程分为分支获取，本地开发，代码提交三个部分。



## 分支获取

###　获取帐号

**这项操作针对每个用户只需要一次。**

我们的gitlab服务器支持LDAP登陆，大家可以使用自己的<strong>内网帐号</strong>登陆gitlab,系统会自动给大家创建帐号。

### 加入项目

**这项操作针对每个项目只需要一次。**

在一项工作开发之前，由于我们的代码管理策略中，不允许非项目相关人员接触代码，因此你刚刚进入gitlab的时候只能看到权限为Public的代码。

你需要向项目管理人员，项目拥有者进行申请，然后会被加入项目。

### 获取代码

在本地没有代码的情况下，一般采取直接使用全局clone来获取远程代码，对于我们的项目来说，当我们获取到远程代码的时候，本地是处于develop分支：

```shell
 git clone git@example.com:project-name.git
```
项目开发一般是基于一个已经在维护中的项目，一般一个项目就意味着一个需求，例如VR500项目和VR500的某个定制单项目，尽管他们之间代码非常接近，甚至仅有配置文件的区别，但是因为最后需要发布的软件是不一样的，所有应当属于不同的项目。

### 切换到自己的分支
```shell
 git checkout -b $feature_name
```
分支可以由管理人员来创建，这样有利于遵循统一的命名规范。各个分支之间需要主要解耦，防止代码之间出现conflict。

### 对仓库进行开发配置

```shell
 git commit -am "My feature is ready"
```
一般来说，feature branch不属于protected branch，因此开发人员可以随意Push自己的代码，但是对于合理的开发流程，应该是在修改之后增加commit，在确定修改不太有可能反复的时候再Push，减少管理人员的负担。

## 本地开发

4. Push your branch to GitLab:
```shell
 git push origin $feature_name
```

5. Review your code on commits page.
在开发人员的代码Push之后，管理人员就可以对代码进行Review或者指派人员进行Review操作。

6. Create a merge request.
在完成代码的Review之后，就可以发出一个合并的请求，和并请求有时候会带来和原有代码的冲突，这时候需要进行stash和stash pop的操作，避免出现文件内的HEAD指针问题，当然，出现了也没有问题，逐个文件进行解决即可。

7. Your team lead will review the code & merge it to the main branch.
管理人员进行合并操作之后，即可对产品进行发布。

## 代码提交

## 后续操作

项目开发完成并且发布之后，这个项目一般都会进入maintain状态，尽管没有开发需求，但是日常使用，测试会有相应的反馈，并且需要有bug的修复，在修复bug的过程中，每一个release版本都会有一个hotfix分支，所有的bug修复均在hotfix分支上实现，hotfix分支的
意义在于即使产品并未发布，依然能够有较为完善的小版本不断释出，是产品处于维护的状态。此外，还有关于特性增删修改的issue会在产品的issue tracker中进行讨论。主要的善后工作如下：

1. 在hotfix分支上解决严重的Bug
2. 在issue tracker中解决使用问题
3. 在wiki中队产品的迭代changelog进行补充
4. 在项目完全结束或者废弃的时候，为项目打上deprecated标签
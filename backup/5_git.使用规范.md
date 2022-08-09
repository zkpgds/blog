# [git 使用规范](https://github.com/zkpgds/blog/issues/5)

## git分支策略
![image](https://user-images.githubusercontent.com/3361961/183578049-255fb33f-c4e2-44fa-865c-295fb91ecc96.png)

## Git 开发流程规范（试用版）

```为规范Git在版本控制方面的使用，引入工具git-flow进行分支管理```

== 安装 git-flow

- Mac OS X

```brew install git-flow```

- Linux

```apt-get install git-flow```

- Windows（Cygwin）

```wget -q -O - --no-check-certificate https://github.com/nvie/gitflow/raw/develop/contrib/gitflow-installer.sh | bash```

(NOTE)注：建议 Windows 下直接使用 IDE，比较简化……

## IDE

Mac OS X和Windows下，推荐使用 Sourcetree，已经自带 git-flow 的功能

## git-flow 初始化

```git flow init```
## git-flow 分支类型及使用场景

(NOTE)git-flow 的分支类型分为 5 种（暂时忽略 support 分支），即 master（主干）、develop（开发）、feature（功能）、release（发布）、hotfix（补丁）。各分支使用场景如下：

- master（主干）：master 分支上的代码是稳定版，即代码开发完成，经过测试，没有明显的bug，才能合并到master中。请注意永远不要在 master 分支上直接开发和提交代码，以确保 master 上的代码一直可用。
- develop（开发）：develop 分支用作平时开发的主分支，并一直存在。如果修改代码，尽量新建 feature 分支修改完再合并到 develop 分支。如果要提交 develop 分支到远端服务器（github），需要使用 git push origin develop 传统方式提交。
- feature（功能）：当需要新加一个功能时（或者在本地修改bug），需要新建一个 feature 分支。在新建的 feature 分支上进行开发，完成后合并到 develop 分支。如果新加的功能需多人共同完成，把新建的 feature 分支发布出去。
- release（发布）：当开发进行到一定程度，可以发布时，建立一个 release 分支并指定版本号（版本号规则请看后续说明），并将新建的 release 发布出去。开发人员可以对 release 分支上的代码进行集中测试，和修改 bug 。全部完成经过测试没有问题后，将 release 分支上的代码合并到 master 分支和自己的 develop 分支。
- hotfix（补丁）：若在 master 分支上发现 bug 需要紧急修复，新建一个 hotfix 分支并指定版本号，在新建的 hotfix 分支上修复完成，测试通过后，合并到 master 分支和本地的 develop 分支。

## 常用使用说明

初始化 git-flow 后，工作拷贝将自动切换到 develop 分支。

### 增加新功能

```git flow feature start FEATURE_NAME```
(NOTE)这个命令基于 develop 分支创建一个新分支 FEATURE_NAME，并且切换到该分支下。

> 如新增一个用户登录功能：

```git flow feature start login```
(NOTE)会基于目前 develop 分支的代码创建 feature/login 分支，并切换工作拷贝到 feature/login 分支下。

### 完成新功能开发

```git flow feature finish FEATURE_NAME```
(NOTE)这个命令将合并当前 feature 分支的代码到 develop ，并且切换到 develop 分支，再删除 feature 分支。

> 如用户登录功能开发完成：

```git flow feature finish login```
(NOTE)会合并 feature/login 分支下的代码到 develop 分支，将工作拷贝切换到 develop 分支，最后删除 feature/login 分支。

### 发布/获取新功能分支

```
git flow feature publish FEATURE_NAME
git flow feature pull FEATURE_NAME
```
(NOTE)如果需要多人协同开发功能，可将新建的 feature 分支发布到远端服务器（github），其他参与者再把该 feature 分支拉取下来。

### 新建一个发布版本

```
git flow release start RELEASE_VERSION [BASE]
```
(NOTE)该命令使用提供的版本号新建一个 release 分支，BASE 是可选参数，表示基于 develop 的什么版本来建立 release 分支，为 develop 分支的某一个 Hash 值。省略 BASE 表示以最新的 develop 代码来建立 release 分支。

> 如基于最新的 develop 代码新建版本号为 0.1.0 的 release 分支：

```git flow release start 0.1.0```
(NOTE)此命令将新建一个 release/0.1.0 分支，并切换工作拷贝至该分支。

发布/跟踪发布分支
```
git flow release publish RELEASE_VERSION
git flow release track RELEASE_VERSION
```
(NOTE)一般来说，新建 release 分支后要立即发布到远端服务器（github），方便其他开发人员合并代码和修改 bug 。

### 完成发布版本

```git flow release finish RELEASE_VERSION```
(NOTE)当 release 经过合并代码和修改 bug，比较稳定时，就完成发布版本。将该 release 分支的代码合并到 master 分支和本地的 develop 分支，并且使用 release 分支名打一个 tag，最后删除此 release 分支。

> 如完成 release 分支 0.1.0 的开发：

```git flow release finish 0.1.0```
(NOTE)此命令将 release/0.1.0 分支合并到 master 分支和本地 develop 分支，以 0.1.0 为名称打一个 tag，并且删除 release/0.1.0 分支。

### 新建补丁分支（bug 紧急修复）

```git flow hotfix start VERSION [RELEASE VERSION]```
(NOTE)当 master 分支上发现 bug 时，不要直接修改 master 分支上的代码，而是使用该命令建立一个 hotfix 分支，在该 hotfix 分支上修复好 bug，最后合并到 master 分支。VERSION 是补丁的版本号，RELEASE_VERSION 是可选参数，填写 finish release 时的版本号以确定修改哪个 master 的代码版本。

### 完成补丁分支

```git flow hotfix finish VERSION```
(NOTE)此命令将修改过 bug 的代码合并到 master 分支和 develop 分支。

## 版本号命名规则

版本号命名以 [大版本号].[小版本号].[补丁号] 方式命名，其中：

# 大版本号更新表示一次里程碑开发的完成，包含了若干个 feature 的实现。
# 小版本号更新表示一个 feature 的完成。
# 补丁号更新表示发布的 feature 不变，只是修改 bug 。

> 举例：

某次发布是里程碑开发的结束，版本号为 1.0.0
很快，上次发布的版本发现了 bug，紧急修复，再次发布，版本号为 1.0.1
再次发现 bug，修复，重新发布，版本号为 1.0.2
几个星期后，新增了几个功能，再次发布，版本号为 1.1.0
几天后发现新增的功能有 bug，紧急修复，发布，版本号为 1.1.1
再次新增功能发布，版本号为 1.2.0
发现 bug，修复并发布，版本号为 1.2.1
再次完成一次里程碑开发，发布，版本号为 2.0.0
……以此类推
非常特殊的情况下，需要同时发布多个版本，以 [大版本号].[小版本号].[补丁号].[rcN] 的方式命名或者 [大版本号].[小版本号].[补丁号].[rc-FEATURE_NAME] 的方式命名：

1.0.0.rc1 或者 1.0.0.rc-ionic
1.0.0.rc2 或者 1.0.0.rc-native
……

## 参考文档
[Git Flow Cheatsheet](http://danielkummer.github.io/git-flow-cheatsheet/index.zh_CN.html)
[Git Flow 使用笔记](http://fann.im/blog/2012/03/12/git-flow-notes/)
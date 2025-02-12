# [git 使用规范](https://github.com/zkpgds/blog/issues/5)

# Git 规范
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

-------------------------------------------------------第二部分------------------------------------------------------------
# 一些git 命令
- 安装arc(不要使用中文)
- 创建远程分支 FEATURE_Txxxx (默认基于 master)
`git push origin master:FEATURE_T1234`
- checkout 远程分支 FEATURE_Txxxx（不要在该分支上直接 commit）
 `git checkout --track origin/FEATURE_T1234`
- checkout 本地开发分支 Txxxx
`arc feature T1234 // git checkout -b T1234 FEATURE_T1234`

> 不要直接在远程分支上提交代码，使用本地分支 commit & diff

-  在开发分支上提交代码并提审
`git commit -m 'T1234 .... '
   arc diff HEAD^                        // 根据实际情况选择 commit id，默认是 parent`

> commit message 以任务号开头，填具体修改内容摘要
一个 task 可对应多个commit，不需要重复填写任务标题

- 根据审核意见修改代码后，更新diff
`git commit --amend
arc diff --update Dxxxxx`

- 审核代码通过后，将代码发布到远程分支
`arc land --onto FEATURE_T1234`

- 打RC tag
`git tag RC_T1234
git push origin RC_T1234`
- 测试过程中修复 bug，重复上述流程
`git checkout -b T1235 FEATURE_T1234    // 使用BUG任务号
git commit -m 'T1235 .... '
arc diff HEAD^`
- 如果此处有update，要使用如下方法：
`git commit --amend
arc diff HEAD^`
- 如果已经accepted，需要land：
`arc land --onto FEATURE_T1234
git tag RC_T1234_2
git push origin RC_T1234_2`

> 使用BUG任务号提交 bugfix 作为关联，方便查看bug的修复方式

- 测试通过后, 合并 master 分支上的最新 PRD 标签
`git checkout FEATURE_T1234
git pull                     // 确认与远端同步
git log origin/master        // 确认最新的 PRD_Txxxx 标签
git merge PRD_Txxxx          // 使用标签而不是分支
git merge --continue
git push`

> 必需在远程分支上先 pull 再 merge，禁止出现 Merge branch 'xxx' of ssh://...

> 解决直接冲突问题后 continue 完成合并，逻辑冲突另提交 commit 审核

> commit message 包含 PRD_Txxxx 标签名， 保留 Conflicts 冲突文件列表
  Merge tag 'PRD_Txxxx' into FEATURE_T1234
  Conflicts: path/to/file.java

- 在 FEATURE 分支上打 PRD 标签
`git tag PRD_T1234
git push origin PRD_T1234`
- 回归测试后，更新 mater 分支
`git checkout master
git merge PRD_T1234 --ff-only
git push`

> 若 master fast-forward 失败，表示有并发上线，沟通确认 master 上的最新 TAG 是否已完成上线
切回 FEATURE 分支，合并最新的 PRD 标签，评估是否需要再次回归测试

- 上线发布前检查
`git log --pretty=oneline PRD_new..PRD_old                    // 是否有遗漏未合并的commit
git log --pretty=oneline PRD_old..PRD_new | grep -v Merge    // 上线包含的commit，将结果评论至上线TASK`

> 测试复核 PRD_old 是线上正在运行的版本，PRD_new = origin/master
-----------------------------------------------------------------------

-------------------------------------------------------第三部分------------------------------------------------------------
# 安装 arc 
Phabricator是一个代码审查管理，Arcanist是Phabricator配套的Code Review工具,需要配合本地git使用。
##  1.确认PHP是否已经安装
Mac OS X应该已经默认安装了PHP环境。在终端中输入php -v检查是否已安装了PHP环境。
最新版php中有废弃的方法,arc不能正常运行，需要安装旧一些的版本。

安装php7.3
```
brew reinstall php@7.3
brew link php@7.3
```
过程中如果有失败，失败什么安装什么

如果遇到错误：Error: php@7.3 has been disabled because it is a versioned formula!
可以先尝试如下命令：
```
brew tap shivammathur/php
brew install shivammathur/php/php@7.3
过程中如果有失败，失败什么安装什么
```
执行完后再重新执行：
```
brew reinstall php@7.3
brew link php@7.3
```

问题：

Library not loaded: /opt/homebrew/opt/icu4c/lib/libicui18n.70.dylib
解决办法：

1. 执行命令 which brew 得到 brew安装目录 /opt/homebrew/bin/brew
2. cd 到 /opt/homebrew
3. cd 到 /Library/Taps/homebrew/homebrew-core/Formula
4. 执行命令：git log --follow icu4c.rb
5. 找到缺少的版本70对应的commit id
6. 执行命令 git checkout -b icu4c-版本号 commitID
7. 执行命令 brew reinstall ./icu4c.rb


## 2.安装Arcanist
在计划安装目录clone Arcanist代码仓库 
 git clone https://github.com/phacility/libphutil.git 
 git clone https://github.com/phacility/arcanist.git 

然后将两个仓库均切换至 legacy-2019 分支

##3.配置arc的环境变量 
新系统的默认shell应该都是zsh，这里以zsh为例来讲述如何进行配置。
```
cd ~
vi .zshrc
```
在配置文件的末尾加上这一句 
```
export PATH=${PATH}:/路径/arcanist/bin/ 
```
保存后退出VI。

输入命令行：`source .zshrc` 更新环境变量。 

然后输入命令：`arc –-help` 查看配置是否成功。

## 4.设置Phabricator地址
vi /路径/arcanist/.arcconfig ，将  "phabricator.uri": "https://secure.phabricator.com/", 改为  "phabricator.uri": "http://phabricator.huobidev.com/",

配置项目 
打开终端，切换到项目所在目录，运行`arc install-certificate`，按提示添加访问token 
打开[[ http://phabricator.huobidev.com/conduit/login/ | token链接 ]]可以获取到token。

在你的用户根目录下建立.arcrc文件。

执行命令 `vi ~/.arcrc`，输入如下内容后保存（**注意需要替换成你获取到的token**）。


```
{
  "config": {
    "default": "http://phabricator.huobidev.com/",
    "editor": "vim"
  },
  "hosts": {
    "http://phabricator.huobidev.com/api/": {
      "token": "cli-otp356fbifisk3qzfteudxz6ylre"
    }
  }
}
```

## 5.代码审查流程 
### 5.1创建diff，发送给reviewer去review
命令为：`arc diff`

在接下来的界面中，填写一些。规范可以参考：

> 代码提交时，Lint结果状态必须为Lint OK。扫描出的问题需要即时修改后，再提交代码。
Summary中必须附带任务号或者Bug号。 举例： [T68652] yeguolong: 去掉HUSD相关
Test Plan需要写明测试影响范围，并跟测试说明。
修复bug时，为了方便他人查阅代码变动历史，需要将Bug号或者任务号添加到代码注释中去。

标题样式：

<type>: <message>

对格式的说明如下：

type代表某次提交的类型，比如是修复一个bug还是增加一个新的feature。所有的type类型如下：
feat： 新增feature
fix: 修复bug
docs: 仅仅修改了文档，比如README, CHANGELOG, CONTRIBUTE等等
style: 仅仅修改了空格、格式缩进、逗号等等，不改变代码逻辑
refactor: 代码重构，没有加新功能或者修复bug
perf: 优化相关，比如提升性能、体验
test: 测试用例，包括单元测试、集成测试等
chore: 改变构建流程、或者增加依赖库、工具等
revert: 回滚到上一个版本

message代表本次提交内容的描述，必须阐述清楚本次提交的详细变更。

### 5.2 reviewer进行Code Review
打开生成的diff链接，进行Review，可以在里面加上review意见。

### 5.3developer根据意见更新代码
更新代码后，再次运行`arc diff`，可以将diff进行更新。

### 5.4developer上传代码
注意，运行的命令是`arc land`，不是`git push`。



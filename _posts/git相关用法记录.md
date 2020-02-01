---
title: git相关用法记录
date: 2017-03-22 18:48:18
categories: 其他
tags: 其他
---
想要记录一下git相关的用法，用到哪儿记到哪儿，慢慢积累吧

### 如何同步github上fork出来的分支

觉得命令行的方式还是蛮简单的，所以就记下命令行的吧

1. 跟上游仓库同步代码之前，先要配置remote，将其指向上游仓库：
    > git remote add upstream https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git

2. 将当前工作路径切换到clone到本地的需要同步的项目目录下
3. 从上有仓库获取到分支和相关的提交信息，他们将被保存在本地的upstream/master分支下
    > git fetch upstream

4. 切换到本地的master分支
    > git checkout master

5. 把upstream/master分支下的内容合并到本地master分支，这样，本地的master分支便跟上有仓库保持同步了，同时也没有丢失你本地的修改
    > git merge upstream/master

同步后的代码只是保存在本地，还需要push到github上。

### 基于github提pr的流程

1. 首先，要将这个项目fork到自己的账户下。
2. 将fork下来的项目clone到本地，并进行修改
3. 修改之后再push到自己的github账号下
4. 上传之后就可以在自己的账户下看到自己的提交，检查没问题后点击"Pull Request"进行PR的提交

### 撤销通过“git add”命令提交到暂存区的文件

可以通过调用"git reset HEAD <file>"的方式撤销提交到暂存区中的文件

### 使用 ssh 方式每次执行 push/pull 方法都要输入密码的解决办法

通过执行 “ssh-add ~/.ssh/id_rsa” 重新将创建好的ssh key添加进ssh-agent 就好了

### git 拉取远程分支到本地

可以通过如下命令拉取远程分支：

```java
git checkout -b localBranch origin/remoteBranch
```

拉取过程中可能遇到如下报错：
fatal: Cannot update paths and switch to branch 'dev' at the same time.
Did you intend to checkout 'origin/dev' which can not be resolved as commit?

原因是本地并没有 localBranch 这个分支，可以通过 git branch -a 命令来查看本地是否具有这个分支，解决方法如下：

git fitch origin remoteBranch 执行该命令把远程分支拉到本地
git checkout -b localBranch origin/remoteBranch 创建本地分支并切换到新建分支

附上一个比较好的链接：
[Git Community Book中文版](http://gitbook.liuhui998.com/index.html)

#### 参考资料：
[如何同步 Github fork 出来的分支](http://jinlong.github.io/2015/10/12/syncing-a-fork/)

[贡献开源项目的流程](http://gitbeijing.com/fork_flow.html)
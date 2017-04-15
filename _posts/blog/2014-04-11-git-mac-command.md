---
layout:		post
title:		Mac下git常用命令及报错
description: Git 是一个很强大的分布式版本管理工具，它不但适用于管理大型开源软件的源代码（如：linux kernel），管理私人的文档和源代码也有很多优势
category:	blog
---

- git init所有文件来创建新的 Git 仓库

- git diff 显示还没有暂存起来的改动，而不是这次工作和上次提交之间的差异

- git diff --cached 显示已经暂存起来的文件和上次提交时的快照之间的差异

- git add 添加

- git commit 提交 -a 相当于跳过 git add

- git rm --cached readme.txt 把文件从 Git 仓库中删除,但仍然保留在当前工作目录中(可用glog模式)

- git mv 修改名称

- git log 会按提交时间列出所有的更新

git log -p -2
-p 选项展开显示每次提交的内容差异，用 -2 则仅显示最近的两次更新：
git log --since=2.weeks 列出所有最近两周内的提交

- git commit --amend 撤消刚才的提交操作

- git reset HEAD benchmarks.rb 取消已经暂存的文件

- git remote 要查看当前配置有哪些远程仓库

git remote -v显示对应的克隆地址：

- git remote add pb git://github.com/paulboone/ticgit.git添加一个新的远程仓库，可以指定一个简单的名字，以便将来引用

- git pull 将远端分支自动合并到本地仓库中当前分支

- git push origin master 把本地的 master 分支推送到 origin 服务器上

- git remote show origin查看某个远程仓库的详细信息

- git remote rename pb paul 修改pb远程仓库在本地的简称paul

- git remote rm paul 需要移除对应的远端仓库

- git tag 列出现有标签

git tag -l 'v1.4.2.*'

- git branch testing在当前 commit 对象上新建一个分支指针

- git checkout testing 切换到其他分支

- git checkout -b iss53 要新建并切换到该分支(相当于18-19)

- git merge hotfix 进行合并分支

- git branch -d hotfix 删除分支

- git branch 如果不加任何参数，它会给出当前所有分支的清单,分支前的 * 字符表示当前所在的分支

- git branch --merged 查看哪些分支已被并入当前分支

git branch --no-merged 查看尚未合并的工作
git fetch origin 来同步远程服务器上的数据到本地。该命令首先找到 origin 是哪个服务器（本例为 git.ourcompany.com），从上面获取你尚未拥有的数据，更新你本地的数据库，然后把 origin/master 的指针移到它最新的位置上

git checkout -b sf origin/serverfix跟踪远程分支

git branch -D testing 强制删除

## pull下代码的时候报错

	Automatic merge failed; fix conflicts and then commit the result

1.出错场景：
协同开发时，我们从远程服务器上pull下代码的时候，出现以下提示信息：

2.原因分析：
利用git status，输出如下：

	git status
	# On branch master
	# Your branch and 'origin/master' have diverged,
	# and have 2 and 2 different commits each, respectively.
	#
	# Unmerged paths:
	#   (use "git add/rm <file>..." as appropriate to mark resolution)
	#
	#    both modified:      apt/sources.list
	#
	# Changes not staged for commit:
	#   (use "git add <file>..." to update what will be committed)
	#   (use "git checkout -- <file>..." to discard changes in working directory)
	#
	#    modified:   cups/subscriptions.conf
	#    modified:   cups/subscriptions.conf.O
	#    modified:   mtab
	#    modified:   update-manager/release-upgrades
	#
	no changes added to commit (use "git add" and/or "git commit -a")

从git status的结果可以发现：其中sources.list这个文件存在合并冲突

而进一步分析git pull的原理，实际上git pull是分了两步走的，（1）从远程pull下origin/master分支（2）将远程的origin/master分支与本地master分支进行合并

以上的错误，是出在了第二步骤

3.解决方法

方法一：如果我们确定远程的分支正好是我们需要的，而本地的分支上的修改比较陈旧或者不正确，那么可以直接丢弃本地分支内容，运行如下命令(看需要决定是否需要运行git fetch取得远程分支)：

	$:git reset --hard origin/master
	或者
	$:git reset --hard ORIG_HEAD

方法二：我们不能丢弃本地修改，因为其中的某些内容的确是我们需要的，此时需要对unmerged的文件进行手动修改，删掉其中冲突的部分，然后运行如下命令

	$:git add filename
	$:git commit -m "message"

方法三：如果我们觉得合并以后的文件内容比价混乱，想要废弃这次合并，回到合并之前的状态，那么可以运行如下命令：

	$:git reset --hard HEAD

## 参考
[Git帮助手册][1]

[1]: http://git-scm.com/book/zh "Git帮助手册"

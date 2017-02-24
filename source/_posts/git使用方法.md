---
title: git使用方法
date: 2016-04-26 11:10:37
tags: git
categories: 版本管理工具
---

git简介
===
[git官网](https://git-scm.com)有全套教程和说明，有兴趣的自己研究。
如果你不适应命令行，可以使用GUI工具，如[SourceTree](https://www.sourcetreeapp.com/)。
官方文档
===
[中文官方文档](https://git-scm.com/book/zh/v2)。
官方文档非常详细,需要仔细阅读。
常用命令
==
<!-- more -->
* 切换分支
```
git checkout branchName
```
* 合并分支(将branchName合并到当前的分支)
```
git merge --no-ff branchName
```
* 新建分支并切换到新建的分支
```
git checkout -b branchName
```
* 删除分支
```
git branch -d branchName //删除本地的分支
git push <shortname> :branchName //删除远程分支
```
* 追加提交
```
git commit -m 'initial commit'
git add forgotten_file
git commit --amend
```
* 取消暂存
```
git reset HEAD fileName
```
* 撤销对文件的修改(`**不可逆**`)
```
git checkout -- fileName
```
* 查看远程仓库的url
```
git remote -v
```
* 添加新的远程仓库
```
git remote add <shortname> <url>
```
* 拉取远程仓库的内容
```
git fetch <shortname> [branchName] //不会合并，只拉取
git pull <shortname>  [branchName] //拉取并且合并
```
* 推送到远程仓库
```
git push [shortname] [branch-name]
```
* 移除某个远程仓库
```
 git remote rm <shortname>
```
* 重命名远程仓库`git remote rename`
```
git remote rename oldShortName newShortName
```
* 创建标签
```
git tag -a 1.2.0 -m 'my version 1.2.0'

//在之前的某次提交commitId打标签
git tag -a 1.1.9 9fceb02
```
* 推送标签到远程仓库
```
git push <shortname> --tags  //推送所有本地标签
git push <shortname> 1.2.0 //推送特定的标签
```
* 检出到特定的标签`git checkout -b [branchname] [tagname]`
```
git checkout -b branchName 1.2.0
```
* 删除标签
```
git tag -d tagName //删除本地的tag
git push <shortname> --delete tag tagName //删除远程的tag
```
* 设置git别名
```
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
```
* 版本回退
```
git reset --hard HEAD ~N  //N为数字，回退N个版本
```
* 撤销某次提交
```
git revert commitId
```
* 文件权限不纳入版本管理
```
git config core.filemode false
```
* 子模块
```
git submodule add 仓库url  本地路径  //添加子模块
git submodule update —init —recursive //下载带有子模块的repo后
```
* 暂存。当某个任务完成了一半，但突然插进来一个其它的紧急任务，这个时候你又不能将未完成的工作commit，暂存stash就非常有用了。
```
git stash save "some comments for this stash"  //暂存并添加注释，方便后续取出
git stash list   //查看当前所有的暂存
git stash apply stash@{0} //取出最后一次的暂存(具体想取出哪次暂存，通过git stash list查看)
git stash drop stash@{0}  //将最后一次的暂存从暂存列表清除
git stash clear   //清除所有暂存
git stash pop    //取出最后一次暂存，并将其从暂存列表清除
git stash branch newBranch stash@{0}  //取出最后一次暂存并切换到新的分支newBranch，同时将其从暂存列表清除
```
附录
===
查看git的总提交次数(可以作为版本号之类的)
```
git rev-list head | sort | wc -l
```
查看git最后一次提交的hash值
```
git rev-list head | head -1
```

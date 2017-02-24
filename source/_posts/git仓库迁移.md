---
title: git仓库迁移
date: 2016-04-28 11:54:58
tags: git
categories: 小技巧
---

### 说明
如果你想从别的git托管服务器哪里复制一份源代码到新的git托管服务器上，可以通过以下步骤实现。
### git仓迁移
1. 从原地址克隆一份裸版本，比如原版本托管在github上。
```
git clone --bare git://github.com/username/project.git
```
2. 到新的git服务器上创建一个空的新项目，比如gitCafe服务器。
3. 以镜像推送的方式，上传代码到gitCafe服务器上
```
cd project.git
git push --mirror git@gitcafe.com/username/newproject.git
```
4. 删除本地之前克隆下来的裸版本
```
cd ..
rm -rf project.git
```
5. 到新服务器gitCafe上找到clone地址，直接Clone到本地就可以了。
```
git clone git@gitcafe.com/username/newproject.git
```
### 结束
这种方式可以保留原版本库中的所有内容。

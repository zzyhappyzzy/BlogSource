---
title: 多账号SSH配置
date: 2017-06-01 12:05:20
tags: ["git","ssh"]
categories: 版本管理工具
---

如果在同一个git服务器上，你有多个账号（比如你有两个Github账号），就会面临多账号的SSH配置问题。

>以下内容是基于MAC电脑，其他操作系统不在本文讨论范畴，但思路一致。

<!-- more -->

SSH
===
SSH全称`Secure Shell`，是一种建立在应用层和传输层的安全协议，具有用户认证和数据加密传输等优势。相比于ftp、pop、telnet等明文传送数据的协议，更加安全可靠。由于版权和加密算法等的限制，现在大部分使用的是开放源代码的OpenSSH。

配置文件
===
SSH的配置文件，位于当前用户的根目录下，名字为`.ssh`隐藏文件夹，在Terminal输入`cd ~/.ssh`命令即可到该目录（如果提示`.ssh`文件夹不存在，可以自己创建一个，输入命令`cd ~ && mkdir .ssh`即可）。接着输入`ls -al`即可看到该目录下的所有文件。

>如果`.ssh`目录和该目录的子文件夹递归查找都没有id_rsa、id_rsa.pub文件，说明电脑并没有配置过ssh。

单个SSH key配置
===
大部分情况下，一个git服务器，我们都只有一个账号，优先说明这种状况下的SSH配置。    

##### 一、生成密钥

```shell
ssh-keygen -t rsa -C "comment"
```

>-t 参数，指定生成密钥的算法，常见的有rsa和dsa，默认为rsa（rsa可用于数字签名和加密，而dsa只能用于数字签名，不能加密。与服务器的ssh加密连接用的都是rsa）。该参数可用默认缺省值。

>-C 参数，说明文字，一般为相应的git账号，默认为电脑名称。该参数缺省值为电脑名称

>该命令可以简写为`ssh-keygen`即可

生成的密钥默认是存储在.ssh目录下的，一直按回车键即可快速生成key了。

![](/ssh/ssh_key_gen.png)

##### 二、添加密钥

通过pbcopy命令复制公钥

```shell
pbcopy < ~/.ssh/id_rsa.pub
```

>也可以使用`cat ~/.ssh/id_rsa.pub`将公钥输出到命令行查看并复制

将复制的公钥粘贴到相应账号的ssh配置项里即可。下面截图为github添加ssh key的示例。

![](/ssh/ssh_add_github_1.png)

![](/ssh/ssh_add_github_2.png)

##### 三、连接测试

```shell
ssh -T git@github.com
```

>如果出现类似`Hi xxxx! You've successfully xxx`之类的文字，则添加完成。否则，会提示权限不足或一直卡着等其他异常

##### 四、使用ssh登陆验证

```shell
git clone [sshUrl]
```

>可以使用 `ssh user@ip`命令登陆远程主机，eg：远程主机ip为192.168.0.100，存在用户jack，则可以输入`ssh jack@192.168.0.100`登陆远程主机

下面为我配置好ssh后，拉取自己github某个仓库的代码示例截图

![](/ssh/ssh_remote_clone.png)

顺便贴个整个过程的截图，我的`.ssh`目录文件较多，是因为有其他的配置，后面会细说

![](/ssh/ssh_shell_pro.png)

说明
===
这里说的单个ssh key是指一台git服务器只有一个git账号。也就是说，如果你不仅在github有账号，在coding也有账号，甚至团队内部自己搭建了私有的git服务器等，这些服务器都可以公用同一个ssh key的，绝对没有必要创建多个ssh key。你只需要将相同的ssh公钥添加到相应的服务器的账号设置里即可。如果自己不能添加，可以将公钥发给服务器管理人员，让他帮忙添加也行。
>ssh key不仅仅用于git服务器，它还支持远程加密登录和相应的操作。比如，登录云服务器，添加ssh key后，可以免密登录

多个SSH key配置
===
所有通过ssh验证过的IP（域名）都会被系统自动添加到`.ssh`目录下的known_hosts文件里，下次再次连接该IP（域名）时，优先读取该文件里的SSH key。

假如有这样的场景：用户A在Github有一个自己私人账号，在电脑上创建并添加过ssh key。某一天，公司要求A再次创建一个公司账号，然后用公司账号拉取项目。

面临的问题：同一台服务器，不同用户的ssh key是不可能相同的。所以，A之前的ssh key是没法用于公司账号的，必须重新为公司账号创建一个新的ssh key。

那么，这个新创建的ssh key存放在哪？通过ssh认证连接时，怎么向服务器发送正确的私钥来验证用户的正确性？

>如果你之前通过http/https的方式拉取过github代码，输入账号密码成功后，github服务器会将它的公钥发给你电脑，电脑会将该公钥存储在`.ssh`目录下的known_hosts文件里。之后再次通过http获取/上传代码，就可以免密直接拉取/上传代码了。

如果A不想再次创建ssh key，尝试通过http的方式拉取代码，会直接报错（没有权限）。那是因为known_hosts文件里有A自己的私有账号信息，系统默认匹配到相同IP（域名），将A的私有账号发给服务器了，当然会无权限喽。

这个时候，A可以删除known_hosts文件里github对应的那一行记录，然后通过http/https认证。但这样做，想要切换回自己的私有账号，又要再次删除known_hosts文件对应的内容。如此反复，太麻烦。

有没有更简单的方式呢？当然喽！通过创建配置文件的方式。

创建的config文件的内容格式为

```
Host [shortName]
  HostName [domain]
  PreferredAuthentications publickey
  IdentityFile [private keyPath]
  User [user]
  Port [port]
```

`shortName` 简称，因为相同域名下有多个账号，所以简称需要不一样才能区分。通过ssh拉取/上传代码时，需要用相应的shortName替换掉ssh地址的域名，其它保持不变，才能保证ssh调用对应的私钥去登陆验证。

`domain` 对应的服务器域名(IP)

`private keyPath` 私钥绝对路径
 
`user` 用户名。**服务器的用户名**，不是你的用户名。eg：大部分的git服务器用户名都为git

`port` 端口号，默认为22

比如，我在[git.coding.net](https://git.coding.net/)上有两个账号。`.ssh`目录下地文件结构如下：

![](/ssh/ssh_dir_tree.png)

其中config文件内容为：

```
Host coding1
  HostName git.coding.net
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/coding1/id_rsa
  User git
 # Port 22

Host coding2
  HostName git.coding.net
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/coding2/id_rsa
 # User git
 # Port 22
```

>具体ssh key怎么生成，参考前面单个ssh key的过程。提醒下，生成ssh key时是可以指定位置存储的。(指定位置时默认是相对当前路径的，也可以填写完整的绝对路径)

将coding1和coding2对用的公钥分别添加到对应的账号设置里后，验证是否成功，和之前单个ssh key有差别。
之前是直接输入 `ssh -T git@git.coding.net`,现在则是通过简称映射对应的域名和私钥。eg：`ssh -T git@coding2`

>由于coding1配置的User为git，所以测试时，可以简单输入`ssh -T coding1`即可。而coding2没有设置User（注释掉了），则必须输入`ssh -T git@coding2`

配置好多个ssh key后，拉取代码时，需要将ssh的地址做细微修改，才能用正确的用户去登陆验证。就是将对应的域名（IP）换成config文件里配置的shortName即可。

>原本的ssh地址为`git@git.coding.net:momoer/DemoForSSH.git`，需要修改为`git@coding2:momoer/DemoForSSH.git`再取拉取即可。如果配置了User项，ssh地址可以简写为`coding2:momoer/DemoForSSH.git`

友情提示：
>通过ssh拉取的代码，如果之前在`~/.gitconfig`文件里配置过user信息，而又不想用全局的user信息，可以在当前工程里的`.git/config`文件里配置user信息，以便查看commit记录等。

顺便贴个我的DemoForSSH仓库的git配置文件
```
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
	ignorecase = true
	precomposeunicode = true
[user]
	name = zhenzhaoyang
	email = zzyhappyzzy@163.com
[remote "origin"]
	url = git@coding2:momoer/DemoForSSH.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
	remote = origin
	merge = refs/heads/master
```

嗯，大概就这些吧。That's All ~








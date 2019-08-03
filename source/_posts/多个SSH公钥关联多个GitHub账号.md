---
title: 一台电脑生成多个SSH公钥关联多个GitHub账号
copyright: true
comments: true
date: 2019-08-01 10:08:45
tags:
 - Hexo
 - Git
 - SSH
categories: Hexo
description: 填坑啦！！！这两天小编就是因为要出一套 Hexo 系列的教程，需要用到两个GitHub账号，所以一直在坑了爬不出来了，在网上找的教程，又是参差不齐，步步都是坑啊！这里小编终于一步一个脚印的从坑里爬了出来，看到美丽的天空，心情舒畅啊！！！
sticky:
---
### 查看是否存在 SSH key
&emsp;&emsp;首先我们先查看我们本地是否存在 SSH key：
```xml
ls -la ~/.ssh
```
如果存在的有，我们可以另外再生成一个，或者为了好区分，把 `.ssh` 文件夹删掉，我们从新生成，这里小编就以没有 SSH key 为例：

### 生成 SSH key
&emsp;&emsp;执行如下命令:
```xml
# 邮箱1 ：your_email_user1@example.com
ssh-keygen -t rsa -C "your_email_user1@example.com" -f ~/.ssh/id_rsa_user1

# 邮箱2 ：your_email_user2@example.com
ssh-keygen -t rsa -C "your_email_user2@example.com" -f ~/.ssh/id_rsa_user2
```
&emsp;&emsp;执行了上面的两个命令后，我们会在本地用户目录下 .ssh 目录下发现:
>*  `your_email_user1@example.com` 邮箱对应的私钥文件 `id_rsa_user1`、公钥文件 `id_rsa_user1.pub` 
>* `your_email_user2@example.com` 邮箱对应的私钥文件 `id_rsa_user2`、公钥文件 `id_rsa_user2.pub` 
### 通过ssh-add添加密钥至ssh-agent中
&emsp;&emsp;执行如下命令：
-添加本文中的两个密钥：
 -添加邮箱`your_email_user1@example.com` 生成的密钥 ：ssh-add ~/.ssh/id_rsa_user1
 -添加邮箱`your_email_user2@example.com` 生成的密钥 ：ssh-add ~/.ssh/id_rsa_user2
 **注意：**如果我们执行命令失败，那我们先执行如下命令：
 ```xml
ssh-agent bash
 ```
 然后从新执行加入两个密钥的命令。
 ### 添加 SSH 公钥到 GitHub 账号上
 &emsp;&emsp;把 `.ssh` 目录下的其中一个公钥根据生成的邮箱创建的 GitHub 账号进行关联，具体添加小编就不多说了。
 ### 在 `.ssh` 目录下创建并配置 config 文件
 -具体内容如下：
 ```xml
Host user1
	HostName github.com
	User git
	preferredAuthentications publickey
	IdentityFile ~/.ssh/id_rsa_user1
	
Host user2
	HostName github.com
	User git
	preferredAuthentications publickey
	IdentityFile ~/.ssh/id_rsa_user2
 ```
 ### 测试
  -执行如下命令：
```xml
ssh -T user1
Hi user1! You've successfully authenticated, but GitHub does not provide shel l access.

ssh -T user2
Hi user2! You've successfully authenticated, but GitHub does not provide shel l access.
```
这时，说明已经OK了！！！这也是大部分的博客介绍到这里，也是没错的，但是我们是要使用 Hexo 关联 GitHub 写博客的，那么这就不够用的，下面就是见证奇迹的时刻！！！
### 仓库地址
 -对应 user1 的仓库原地址：`git@github.com:githubname/repository.git` ,但是我们要使用时，是这样的：`user1:githubname/repository.git`。
 -另外的一个同样是这样的。
注意：其中的 user1 就是我们在`.ssh` 目录下创建的 config 文件中的 Host 后面的值
&emsp;&emsp;这时我们在博客配置文件中配置如下：
```xml
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: user1:githubname/repository.git
  branch: master
```
这时我们这个本地博客关联的就是 user1 的 GitHub 账号。


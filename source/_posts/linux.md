---
title: Linux 服务器搭建deno
date: 2020-09-23 00:00:00
tags: Linux Deno
---

1.购买服务器

2.**authorized_keys**

https://blog.csdn.net/alifrank/article/details/48241699

https://blog.csdn.net/xlgen157387/article/details/52669709

3.install node

https://blog.csdn.net/qq_38543537/article/details/90899119

2.安装git,配置密钥

yum install git

```
#1）生成密钥，一路回车、用默认选项
ssh-keygen -t rsa              
 
#2）复制到粘贴板
cat ~/.ssh/id_rsa.pub | clip    # Windows
cat ~/.ssh/id_rsa.pub | pbcopy  # MacOS

#3)
github add ssh key
```

4.为了拉取github上的代码

重置git配置：用户名与密码

https://www.cnblogs.com/shenxiaolin/p/7896489.html

4.部署

解决部署时：Host key verification failed.的问题：ssh-keyscan -t rsa github.com >> ~/.ssh/known_hosts

https://stackoverflow.com/questions/13363553/git-error-host-key-verification-failed-when-connecting-to-remote-repository

5.commit or stash your changes before deploying

解决办法：pm2 deploy pm2.json production --force

https://github.com/Unitech/pm2/issues/2758

6.安装deno，建立软连接

![image-20200923152541695](/Users/wangzongyao/Library/Application Support/typora-user-images/image-20200923152541695.png)
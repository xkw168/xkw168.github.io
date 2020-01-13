---
layout: post
title:  "Ubuntu设置ssh key登录"
date:   2020-01-13
categories: Coding
tag: 奇技淫巧
---

* content
{:toc}
很多人可能都遇到过这样的场景，每次远程ssh登录服务器都需要输入一次密码，既不安全又麻烦，让我们来一起看看如何使用公钥私钥避免输入密码登录吧。

## Step1 本地生成密钥

打开Terminal，输入指令`ssh-keygen`，会询问你文件名，自己起一个名字，然后让你设置passphrase，这个是在密钥的基础上多加一重保障（设置了之后，本地使用私钥ssh登录，会要求先输入这个密码），为了方便，我们不设置这个（留空就好）

![image-20200113130515987](/img/ssh1.png)

设置完成后，会出现以下界面

![image-20200113130726631](/img/ssh2.png)

当前文件夹会生成test（私钥）和test.pub（公钥）两个文件

## Step2 上传公钥文件

下一步上传并设置公钥文件（利用copy-ssh-id这个工具，很多操作系统都默认安装了）

```
ssh-copy-id -i identity_file username@remote_host
```

这里的identity_file就是你在Step1里面生成的**公钥**文件（注意是公钥！！！）

成功之后，打开服务器，进到ssh路径下（`cd ~/.ssh`），你会发现有一个authorized_keys文件，查看该文件，你会发现就是你本地的公钥文件(.pub)

## Step3 利用密钥ssh登录

设置好密钥之后，下次就可以直接用

```
ssh -i identify_file username@remote_host
```

登录你的服务器了，这里的identify_file是你在Step1里面生成的**私钥**文件（注意和ssh-copy-id不一样）

为了方便，你还可以将这个命令保存到.bashrc(或.zshrc)等系统配置文件里面，利用alias指令，这样以后你就可以用connect代替那一长条指令了。

```
alias connect='ssh -i identify_file username@remote_host'
```


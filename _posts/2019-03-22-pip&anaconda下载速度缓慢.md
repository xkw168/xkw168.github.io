---
layout: post
title:  "pip / anaconda修改下载源提高下载速度"
date:   2019-03-22
categories: Python
tag: 小技巧
---

* content
{:toc}


&emsp;&emsp;pip和anaconda是两款十分优秀的python包管理器，到那时由于他们的默认源都是在国外，经常会出现下载速度缓慢甚至是连接断开导致失败，所以这里分别介绍一下如何将其默认下载源改为国内的镜像（以清华大学镜像源为例）。但有些时候不排除国内镜像上不去等情况，这时候可能会想回到默认镜像，这里也提供了相应的方法

## anaconda
永久修改

```
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
```

换回默认镜像源

```
conda config --remove-key channels
```

## pip

临时使用

```
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple some-package
```

永久修改

```
pip install pip -U
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

换回默认源

```
pip config set global.index-url https://pypi.org/simple/
```

## 国内镜像源

豆瓣：http://pypi.douban.com/

华中理工大学：http://pypi.hustunique.com/

山东理工大学：http://pypi.sdutlinux.org/

中国科学技术大学：http://pypi.mirrors.ustc.edu.cn/

阿里云：http://mirrors.aliyun.com/pypi/simple/

清华大学：https://pypi.tuna.tsinghua.edu.cn/simple/

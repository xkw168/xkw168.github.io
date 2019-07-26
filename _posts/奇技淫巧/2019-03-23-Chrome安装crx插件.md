---
layout: post
title:  "Chrome 73安装crx插件"
date:   2019-03-23
categories: 其他
tag: 奇技淫巧
---

* content
{:toc}


chrome升级到73之后，插件的安装已经不支持直接拖拽进行安装，需要用一些其他的方式，这里记录一下我的踩坑日记

1. 首先下载任意一个第三方插件（.crx格式）
2. 然后将后缀改为.zip
3. 解压成一个文件夹，然后Chrome->更多工具->拓展程序->加载已解压的扩展程序（或者直接将文件夹拖进去）

这里我主要遇到一个问题，因为我是mac系统，然后用自带软件解压zip会解压出来.zip.cgrx文件，用The Unarchiver解压会报错，最后我选择了命令行的形式，首先cd到.zip文件所在目录，然后利用unzip命令进行解压
`unzip -d exDir (name).zip`

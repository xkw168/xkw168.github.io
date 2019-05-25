---
layout: post
title: 基于github+jekyll的博客搭建
permalink: /blog/
---

* content
{:toc}


http://jekyllthemes.org/

使用
====================================

下载
------------------------------------

使用git从[LessOrMore](https://github.com/luoyan35714/LessOrMore.git)主页下载项目

``` bash
git clone https://github.com/luoyan35714/LessOrMore.git
```

配置
------------------------------------

`LessOrMore`项目需要配置的只有一个文件`_config.yml`，打开之后按照如下进行配置。

> 特别注意`baseurl`的配置。如果是`***.github.io`项目，不修改为空''的话，会导致JS,CSS等静态资源无法找到的错误

``` bash
name: 博客名称
email: 邮箱地址
author: 作者名
url: 个人网站
# baseurl修改为项目名，如果项目是'***.github.io'，则设置为空''
baseurl: "/LessOrMore"
resume_site: 个人简历网站
github: github地址
github_username: github用户名称
# 请到百度统计官网[https://tongji.baidu.com/](https://tongji.baidu.com/)申请自己的网站ID并在此处替换，否则将无法正常统计访问量
baidu_analysis: 94be4b0f9fc5d94cc0d0415ea6761ae9
# 请到revolvermaps [http://www.revolvermaps.com/?target=setupgl](http://www.revolvermaps.com/?target=setupgl)申请自己的网站ID并在此处替换，否则将无法正常统计访问量
revolvermaps: 5ytn1ssq6za
```

关于统计
------------------------------------

本项目支持三种统计，分别是

+ [百度统计](https://tongji.baidu.com)

百度统计是后台统计，并没有再页面有任何展示，但是可以通过登录百度统计官网查看更详细的访问记录分析。
当Fork本项目之后需要去百度统计官网申请自己的baidu统计ID替换 `_config.yml` 文件中的 `baidu_analysis`。

+ [revolvermaps地图统计](http://www.revolvermaps.com/)

revolvermaps地图统计是展示在左侧当行栏的地图展示，具体展示形式可以去官网定制。
当Fork本项目之后需要去revolvermaps地图官网申请自己的统计ID， 替换`_config.yml` 文件中的 `revolvermaps`。

+ [不蒜子统计](http://busuanzi.ibruce.info/)

不蒜子统计是出现在页面右上角的`本站总访问量n次`统计，本统计会自动识别客户所对应的域名，根据不同域名统计，所以并不需要Fork本项目者做任何修改。
更多不蒜子的展示方式可以去官网查看。


如何写文章
------------------------------------

在`LessOrMore/_posts`目录下新建一个文件，可以创建文件夹并在文件夹中添加文件，方便维护。在新建文件中粘贴如下信息，并修改以下的`titile`,`date`,`categories`,`tag`的相关信息，添加`* content {:toc}`为目录相关信息，在进行正文书写前需要在目录和正文之间输入至少2行空行。然后按照正常的Markdown语法书写正文。

``` bash
---
layout: post
#标题配置
title:  标题
#时间配置
date:   2016-08-27 01:08:00 +0800
#大类配置
categories: document
#小类配置
tag: 教程
---

* content
{:toc}


我是正文。我是正文。我是正文。我是正文。我是正文。我是正文。
```

执行
------------------------------------

``` bash
jekyll server
```

效果
------------------------------------
打开浏览器并输入URL`http://localhost:4000/`,回车。
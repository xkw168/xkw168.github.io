---
layout: post
title: 基于github+jekyll的博客搭建
permalink: /blog/
---

* content
{:toc}


最近趁着有点时间，完成了一件自己一直想做的事情，搭建一个个人博客！下面记录一下大致流程，本篇内容适合小白用户，大神可选择性跳过。

---

## GitHub新建仓库

1、点击右上角“➕”，新建仓库

![add](/img/github_blog/add&#32;rep.png)

2、仓库名为`github用户名.github.io`，设置为共有，使用README初始化

![create](/img/github_blog/create&#32;rep.png)

3、新建仓库之后，点击进入`Setting`

![setting](/img/github_blog/setting.png)

4、一直向下划，直到github pages，字段含义如图

![pages](/img/github_blog/pages.png)

---

## 添加模板

推荐一个jekyll的[模板网站](http://jekyllthemes.org/)

1、找到喜欢的模板，整个下载下来（可以先预览一下）

![template](/img/github_blog/template.png)

2、利用git将仓库代码克隆到本地

在github仓库首页，点击`clone and download`，复制地址（利用git）或者点击`Open in Desktop`（利用Github Desktop），下面只介绍用git命令行的方法，Github Desktop的方法请自行google。

![clone](/img/github_blog/clone.png)

``` bash
cd /目标文件夹
git clone “刚刚复制的地址”
```

3、复制模板代码到本地仓库

ctrl+c, ctrl+v

4、提交并上传

``` bash
git add --all
git commit -am "提交信息"
git push
```

5、测试结果

成果提交代码之后，回到github的该仓库，检查是否已经出现了最新的文件，并访问`github用户名.github.io`网址进行测试
![file](/img/github_blog/file&#32;list.png)

---

## 模板使用

初步搭建起个人博客网站，接下来就要修改模板，以符合自己的需求。
注：以下内容以LessOrMore模板为例，节选自其README文件，有删改

### 配置

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

### 关于统计

本项目支持三种统计，分别是

- [百度统计](https://tongji.baidu.com)

百度统计是后台统计，并没有再页面有任何展示，但是可以通过登录百度统计官网查看更详细的访问记录分析。
当Fork本项目之后需要去百度统计官网申请自己的baidu统计ID替换 `_config.yml` 文件中的 `baidu_analysis`。

- [revolvermaps地图统计](http://www.revolvermaps.com/)

revolvermaps地图统计是展示在左侧当行栏的地图展示，具体展示形式可以去官网定制。
当Fork本项目之后需要去revolvermaps地图官网申请自己的统计ID， 替换`_config.yml` 文件中的 `revolvermaps`。

- [不蒜子统计](http://busuanzi.ibruce.info/)

不蒜子统计是出现在页面右上角的`本站总访问量n次`统计，本统计会自动识别客户所对应的域名，根据不同域名统计，所以并不需要Fork本项目者做任何修改。
更多不蒜子的展示方式可以去官网查看。

### 如何写文章

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

### 执行

``` bash
jekyll server
```

### 效果
打开浏览器并输入URL`http://localhost:4000/`,回车。

### 关键文件介绍

- `_includes/header.html` —— 控制网页抬头（页眉）
- `_includes/footer.html` —— 控制网页页脚
- `_includes/lefttree.html` —— 控制侧边栏（那个地图）
- `styles/images/favicon.jpg` —— 浏览器页面的那个小图标
- `styles/images/logo.jpg` —— 博客左上角的LOGO

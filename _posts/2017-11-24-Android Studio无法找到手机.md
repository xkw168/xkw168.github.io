---
layout: post
title:  "Android Studio无法找到手机"
date:   2017-11-24
categories: 其他
tag: 小技巧
---

* content
{:toc}


&emsp;&emsp;刚开始接触Android开发，使用的是Android Studio软件，代码敲起来是真的爽，而且界面什么的也做的很漂亮，但是经常会遇到一个奇葩的问题就是烧程序的时候找不到手机........这就非常尴尬了，好不容易找到了解决方法，放在这里供大家一起借鉴。

---

## Method1：重启手机和电脑
此方法最简单，但是大多情况下基本没有效果。

## Method2：重启ADB
此方法虽然相较复杂，但大多数情况下都能有效

&emsp;&emsp;首先打开Tool->Android->Android Device Monitor（可能会有点慢，别急，慢慢等待....）

　　![这里写图片描述](http://img.blog.csdn.net/20171124115142838?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2V3ZWkxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

&emsp;&emsp;打开后界面如下，点击小三角，重启ADB

　　![这里写图片描述](http://img.blog.csdn.net/20171124115507831?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2V3ZWkxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

&emsp;&emsp;如无意外，应该会弹出如下界面重启失败......（要是成功了就恭喜你了！）

　　![这里写图片描述](http://img.blog.csdn.net/20171124115616420?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2V3ZWkxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

&emsp;&emsp;这种情况一般是电脑的ADB端口被占用（多数是安装的手机助手软件），此时打开任务管理器（开始菜单上右键或者ctrl+alt+.），找到相应的手机助手进程，关闭（此处我使用的是360手机助手）

　　![](http://img.blog.csdn.net/20171124115833580?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2V3ZWkxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

&emsp;&emsp;关闭了相应的进程之后，我们再次打开刚才的Android Device Monitor，点击重启ADB

　　![这里写图片描述](http://img.blog.csdn.net/20171124120101935?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2V3ZWkxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

&emsp;&emsp;重启成功，此时再到Android Studio里面点击烧写代码

　　![这里写图片描述](http://img.blog.csdn.net/20171124120201957?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2V3ZWkxNjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

&emsp;&emsp;发现找到设备了哎，大功告成，万岁！！！

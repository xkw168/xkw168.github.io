---
layout: post
title:  "Android Studio导入Eclipse工程 encoding报错"
date:   2017-12-09
categories: Java
tag: Android开发
---

* content
{:toc}


## 问题描述

---
&emsp;&emsp;Android Studio导入Eclipse工程的时候，假如原来Eclipse工程使用的是GBK编码，在Studio里面编译会报一大堆看不懂的关于encoding的错误。

## 解决方案

---

&emsp;&emsp;在build.gradle里面添加compileOptions，并添加编码“GBK”

```gradle
apply plugin: 'com.android.application'

android {
    compileSdkVersion 18
    buildToolsVersion "26.0.2"

    compileOptions{//添加编码GBK
        encoding "GBK"
    }

    defaultConfig {
        applicationId "com.joypad"
        minSdkVersion 18
        targetSdkVersion 18
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.txt'
        }
    }
}
```

&emsp;&emsp;添加完之后重新编译，之前的错误就都没有啦。

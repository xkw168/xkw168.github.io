---
layout: post
title:  "非AppCompactActivity使用Material Design组件"
date:   2018-07-23
categories: Java
tag: Android开发
---

* content
{:toc}


&emsp;&emsp;使用Material Design的组件有一个要求就是程序必须要是AppCompatActivity或者是继承自AppCompat主题，然鹅我们的程序千奇百怪，怎么可能全都满足这样的情况呢，那么在其他情况我们应该如何使用Material Design组件呢？毕竟这么好的东西不用浪费了。  
&emsp;&emsp;关键在于单独设置组件的theme属性就好，下面以actionchip为例，只要将theme设置为Theme.AppCompat即可

```xml
<android.support.design.chip.Chip
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/chip_template"
    android:layout_width="wrap_content"
    android:layout_height="@dimen/large_chip_height"
    android:textSize="@dimen/panel_suggestion_content_text_size"
    android:backgroundTint="@color/SuggestedActionChipColor"
    android:theme="@style/Theme.AppCompat"
    style="@style/Widget.MaterialComponents.Chip.Action"/>
```

----------
补充一个知识就是theme和style的区别在哪里（引用自Stack Overflow）

> When you apply a style to a single View in the layout, the properties defined by the style are applied only to that View. If a style is applied to a ViewGroup, the child View elements will not inherit the style properties—only the element to which you directly apply the style will apply its properties. However, you can apply a style so that it applies to all View elements—by applying the style as a theme.

简单来说就是style属性只针对应用的单个组件，但是theme属性会影响应用的组件和其包含的子控件

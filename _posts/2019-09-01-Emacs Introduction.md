---
layout: post
title:  "Emacs入门"
date:   2019-09-02
categories: Coding
tag: Editor
---

* content
{:toc}

&emsp;&emsp;最近由于课程要求，不得不开始学习Emacs的使用，开个博客记录一下Emacs痛苦的入门之路，简单记录一下常用的快捷键。持续更新ing...

注：关于Emacs和Vim的区别，[这里](https://mp.weixin.qq.com/s/KTaEuy7kfm7t1bwQmvE32Q)有一篇很好的文章

|                            Command                            | Shortcut |
|:-------------------------------------------------------------:|:--------:|
| Open("visit") file                                            | C-x C-f  |
| Save current buffer(an open file)                             | C-x C-s  |
| Save as [another name]                                        | C-x C-w  |
| Quit Emacs                                                    | C-x C-c  |
| Suspend Emacs                                                 | C-z      |
| Resume Emacs(when it is suspended)                            | fg       |
| Undo                                                          | C-_      |
| Set mark(then you can select a region text using your cursor) | C-@      |
| Cut(Kill)                                                     | C-w      |
| Copy                                                          | M-w      |
| Paste                                                         | C-y      |
| Page down(up)                                                 | C-v(M-v) |

搜索的功能相信是很多人日常的需求

Search word in Emacs:

|                    Command                    |                       Shortcut                       |
|:---------------------------------------------:|:----------------------------------------------------:|
| Enter isearch mode                            | M-x isearch-forward (then you can enter the keyword) |
| Jump to next occurrence                       |                          C-s                         |
| Jump to previous occurrence                   |                          C-r                         |
| Exit and place the cursor at origin position  |                          C-g                         |
| Exit and place the cursor at current position |                         Enter                        |

---
layout: post
title: "Crosstool-ng Notes"
date: 2013-05-15 11:14
comments: true
tags: Toolchain
---
最近的项目需要在x86的机器上编译一些arm程序，而之前编译好的arm交叉编译器要么是Android专用的，要么对内核版本的要求比较高，静态编译出来的程序在较低版本的内核上没法运行。所以今天就再用crosstool-ng重新编译了一次工具链。

crosstool-ng是一个用于构建交叉编译器的工具，替代了以前的crosstool，而且这个项目目前还处于比较活跃的状态中。目前最新的版本是2013-01-31发布的1.18.0版本。[crosstool-ng官方网站][crosstool-ng]

在Google里搜索crosstools-ng，第二条就会出现一些教程，但是这些教程已经比较老了。一些源码包的版本号跟博主当时的需求相关紧密，并不适用于我的情况。于是，我就到crosstool-ng官网下载了源码包，自己一步步看自带的说明文档。crosstool-ng的源码包中的文档位于doc目录，里面的讲解非常详细，按照步骤一步一步做下来基本上没有什么障碍。不过，还是有一些小技巧可以和大家分享分享。

1. crosstool-ng 1.18.0版已经可以通过基于ncurses的图形菜单来配置了。首先根据doc中的说明，安装好ct-ng。然后`ct-ng menuconfig`就可以启用图形菜单了。不过需要注意的是，最好每一项都确认一下，因为默认的设置并不一定适合你的需求。
2. crosstool-ng默认使用的软件包下载源比较慢，可以设置使用镜像网站。设置方法：Path and misc options-->Use a mirror，然后更改默认的`http://crosstool-ng.org/mirrors` 为 `http://mirrors.ustc.edu.cn/gnu` 。大部分的软件源码包都可以在这个源下下载到。另外，linux-kernel的源码包也可以在`http://mirrors.ustc.edu.cn/linux-kernel`目录下去下载。在此，特别感谢USTC(中国科学技术大学)和USTC LUG。
3. 为了避免静态编译出来的程序在较老的内核上报'kernel too old'的错，可以在Operating Syste-->Target OS选则Linux，然后再选择Linux kernel version。

[crosstool-ng]: http://crosstool-ng.org/

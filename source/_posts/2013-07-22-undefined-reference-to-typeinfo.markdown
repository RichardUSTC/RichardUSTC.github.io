---
layout: post
title: "Undefined reference to typeinfo"
date: 2013-07-22 14:34
comments: true
categories: CPP
---
在项目中遇到了这样一个问题：C++文件编译都OK，但链接的时候报错：undefined reference to `typeinfo for xxx'。typeinfo是C++中的RTTI(RunTime Type Identification)机制中记录类型信息用的，dynamic_cast和typeid操作符会使用这些信息。

以"undefined reference to typeinfo"为关键字在网络上搜索，大多数都是说有虚函数定义了但是未实现导致的。但是我的代码显然不是这个情况。在我即将放弃的时候，终于在StackOverflow上发现有人提出，这种错误的原因也可能是混合使用了带RTTI信息和不带RTTI信息的代码导致的。对比检查，发现我的项目里的问题正是这个。最后用了一点dirty hack，解决了bug。下面就仔细分析一下"undefined reference to `typeinfo for xxx'"产生的原因。

## 虚函数未实现
产生"undefined reference to `typeinfo for xxx'"最常见的原因就是基类的虚函数未实现了。由于C++类的实现可以分布在多个源文件中，所以生成目标文件时，基类的虚函数没有定义是不会报错的。但是链接成可执行文件时，需要将虚函数的信息放进typeinfo中，这个时候虚函数未实现就会引发这个错误。

## 混用了no-RTTI代码和RTTI代码
我碰到的正是混用了no-RTTI和RTTI代码的情形。项目中我们自己写的程序必须开启RTTI，而我们使用的外部的一个库使用no-RTTI编译。我们在自己的代码中需要重载一个外部库中的带虚函数的类，结果链接的时候就出现了问题。外部库中的基类使用-fno-rtti选项编译，生成的代码没有typeinfo信息，而我们的代码使用-frtti选项编译，要求基类必须要有typeinfo信息。最后，我在编译系统中做了一些dirty hack，让那个派生类所在的源文件以-fno-rtti选项编译，解决了问题。
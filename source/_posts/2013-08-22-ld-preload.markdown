---
layout: post
title: "LD_PRELOAD的作用"
date: 2013-08-22 15:00
comments: true
tags: Toolchain
---
LD_PRELOAD用于指定提前加载一些动态库，这些动态库比libc.so等库装载更早，它们提供的函数能够屏蔽后加载的动态库中的函数。这个特性可以方便地用来截获库函数调用。

例如，有一个已经编译好的程序使用malloc分配内存，你想使用Google开发的tcmalloc来提升效率，[使用LD_PRELOAD可以实现这个目的][tcmalloc]。


下面举一个小例子来说用LD_PRELOAD的使用。
## 源文件
```c malloc.c
#include <stdio.h>
typedef unsigned long size_t;
void *malloc(size_t size){
    printf("malloc(%ld)\n", size);
    return NULL;
}
```

```c test.c
#include <stdio.h>
#include <stdlib.h>
int main(){
    printf("start.\n");
    malloc(10);
    printf("end.\n");
    return 0;
}
```
## 运行
首先，使用普通方法运行。
```bash
gcc test.c -o test
./test
```
程序输出：
```
start.
end.
```

接下来，讲malloc.c编译成动态链接库
```bash
gcc -fPIC -shared malloc.c -o malloc.so
LD_PRELOAD=./malloc.so ./test
```
程序输出：
```
start.
malloc(10)
end.
```
可以看出，使用LD_PRELOAD之后，我们自定义的malloc取代了库函数中的malloc。

[tcmalloc]: http://goog-perftools.sourceforge.net/doc/tcmalloc.html
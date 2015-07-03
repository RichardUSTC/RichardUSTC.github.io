---
layout: post
title: "mmap x86小于0x10000的虚地址"
date: 2013-05-21 22:03
comments: true
categories: Kernel
---
我们的项目比较特殊，需要在x86机器上mmap小于0x10000的虚地址。然而在默认的x86 Linux上，小于0x10000的虚地址是没有办法mmap的。而x86 Linux不让用户mmap低于0x10000的虚地址也是有它的道理的。

有时候因为某些设计缺陷，程序使用了错误的指针，访问了错误的内存。如果指针指向的地址在进程中没有被映射，那么执行时会产生page fault异常，操作系统会给进程发送SIGSEGV信号。如果程序没有注册SIGSEGV信号处理函数，接收到这个信号就会退出，并报告我们熟悉的'Segmentation Fault'。但是，如果指针指向的地址在进程中被映射了，错误的内存访问就会悄无声息地发生，给debug带来了很大的难度。在设计进程的虚拟空间的时候，x86 Linux不让用户mmap低于0x10000的虚地址空间，那么这段空间永远不会被映射，所有非法访问这段内存的行为都会被发现，为程序debug带来了一些便利。

其实，在不同的Linux系统下，默认可以mmap的最低地址是不同的。比如x86上是0x10000，mips上是0x4000，arm上是0x1000。

下面这个小程序能够输出当前系统下可以mmap的最低地址：
```c mmap.c
#include <sys/mman.h>
#include <stdio.h>
#include <unistd.h>

int main(){
    int i;
    void *start = NULL;
    size_t page_size = getpagesize();
	while(1){
		void *addr = mmap(start, page_size, PROT_READ|PROT_WRITE, \
		MAP_PRIVATE|MAP_FIXED|MAP_ANONYMOUS, -1, 0);
		if(addr != MAP_FAILED) {
			printf("lowest mapable address: %p\n", start);
			break;
		}
        start += page_size;
	}
	return 0;
}
```

我们的项目必须要能够在x86 Linux中mmap 0x1000到0x10000这段地址，经过一番摸索，我终于找到了解决办法。Linux Kernel中有一个参数`mmap_min_addr`控制着mmap能够映射的最低内存地址，而这个参数能够通过`/proc/sys/vm/mmap_min_addr`这个文件[1]来进行读写。系统中的任何用户都可以读这个文件，但只有root用户能够写这个文件。
```bash
# echo 4096 > /proc/sys/vm/mmap_min_addr
```
执行完这条命令之后，再用上面的小程序测试可以mmap的最低地址就变成了0x1000，实现了我们的需求。

在重新启动Linux之后，`mmap_min_addr`就会重新被设置为默认值。要想永久改变`mmap_min_addr`的值，可以在/etc/sysctl.conf中添加一行
```
vm.mmap_min_addr = 4096
```

[1] 其实这个文件是一种特殊的文件，其文件大小为0，读取文件内容时，系统调用read会触发内核中的代码自动生成内容填写给用户态buffer中。写文件内容时，系统调用write会触发内核中的代码自动从用户态buffer读取内容，设置相应的内核数据。内核模块的参数也可以通过这种文件的方式来读取和设置。
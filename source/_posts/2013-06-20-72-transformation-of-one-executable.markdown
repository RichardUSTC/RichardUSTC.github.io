---
layout: post
title: "一个可执行文件的72变"
date: 2013-06-20 17:21
comments: true
categories: Other
---
同一个可执行文件，改成不同的名字，就拥有不同的功能？

我最早看到的这种拥有“72变”能力的可执行文件就是大名鼎鼎的`busybox`。
经过一番思考，我弄明白了其中的原理，现在在这里剖析一下。
先来看一看一个简单的test.c
```c test.c
#include <stdio.h>
int main(int argc, char *argv[]){
	printf("%s\n", argv[0]);
	return 0;
}
```
编译运行
```
$ gcc test.c -o test
$ ./test
./test
$ cp test test1
$ ./test1
./test1
```
之所以会产生这样的结果是因为传给main的参数`argv`中的`argv[0]`始终存储着可执行文件的路径。利用这个特性，我们就可以写出一个拥有“72变”能力的程序了。

下面给出一个拥有“3变”能力的例子。
```c 3.c
#include <stdio.h>
#include <libgen.h>
#include <string.h>

int main(int argc, char *argv[]){
	//get the name of the executable
	char *path = strdup(argv[0]);
	char *bname = basename(path);

	if(!strcmp(bname, "happy")){
		printf("I'm happy!\n");
	}
	else if(!strcmp(bname, "unhappy")){
		printf("I'm not happy!\n");
	}
	else{
		printf("To be, or not to be, that is the question.\n");
	}
	return 0;
}
```
编译运行：
```
$ gcc 3.c -o happy
$ ./happy
I'm happy!
$ mv happy unhappy
$ ./unhappy
I'm not happy!
$ mv unhappy x
$ ./x
To be, or not to be, that is the question.
```

Shell脚本也可以玩这一招
```sh 3.sh
#!/bin/sh

bname=`basename $0`
if [ $bname = "happy" ]
then
    echo "I'm happy!"
elif [ $bname = "unhappy" ]
then
    echo "I'm not happy!"
else
    echo "To be, or not to be, that is the question."
fi
```
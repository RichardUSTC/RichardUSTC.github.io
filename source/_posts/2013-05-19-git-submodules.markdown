---
layout: post
title: "Git Submodules"
date: 2013-05-19 21:22
comments: true
tags: Git
---
有时候，一个项目可能需要使用一些外部的源码，或者多个项目共享一部分相对独立的代码。这个时候，不适宜直接把这些外部源码或者共享的源码包含在当前项目的源码中，最好能够独立管理它们。而git的submodule就正好提供了这个功能。这里举一个简单的示例。

假设当前项目名为project_a，需要使用一个独立的project_b的代码。假设project_a和project_b的代码仓库都已经创建好。可以这样初始化：
```bash
git clone <project_a-repo> a
cd a
git submodule add <project_b-repo> b
```
这时，在目录a下就会生成一个.gitmodules的文件，文件内容为：
```
[submodule "b"]
	path = b
	url = <project_b-repo>
```
同时目录a下会生成一个目录b，其中包含的正是project_b的内容。
添加需要提交的文件之后，使用git commit提交更改，使用push将project_a的更改推送到<project_a-repo>上。
现在，我们再在另外一个位置clone出<project_a-repo>，查看添加submodule的效果。
```bash
git clone <project_a-repo> a
cd a
```
在目录a下可以发现有一个目录b，但是目录中还没有project_b的内容。
现在可以用下面的命令来获取project_b的内容。
```bash
git submodule init
git submodule update
```
命令执行完成之后，project_b的内容就clone到目录b下了。

此时需要注意的是，每次git submodule update执行完之后，目录b中的working tree没有指向任何的branch，需要自己使用git checkout切换到已有的分支上，否则所做修改可能丢失。

以后，当project_b的内容发生变化之后，在目录b下用git pull或git fetch就可以获取project_b的变化了。
需要注意的是，当目录b改变之后，需要先将目录b提交之后，才能提交目录a的更改。
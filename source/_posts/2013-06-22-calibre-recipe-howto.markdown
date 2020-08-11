---
layout: post
title: "如何使用Calibre Recipe"
date: 2013-06-22 21:57
comments: true
tags: Other
---

Calibre Recipe是Calibre用来抓取网页或RSS并制作成电子书的脚本。Calibre Recipe的使用方法有两种：
## 图形界面下使用

### 添加Recipe
1. 首先打开Calibre
2. 点击“抓取新闻”*右边的的“v”*，选择添加“自定义新闻源”
3. 点击“切换到高级模式”
4. 将下载的Recipe文件的内容复制到高级模式下的文本框里，完全覆盖原来的内容
5. 点击“添加/更新订阅清单”
6. 关闭，选“是”即可

### 下载转换
1. 在Calibre主界面点击*“抓取新闻”*
2. 选择“自定义”
3. 选择要下载的条目，点击右下角的“立即下载”
4. 或者勾选“计划下载”，设置按计划下载。

## 命令行下使用
在安装了Calibre的机器上，可以使用如下命令来使用Recipe下载并转换电子书：

假设Recipe的名称为myrecipe.recipe。

生成mobi
```
ebook-convert myrecipe.recipe .mobi
```
生成epub
```
ebook-convert myrecipe.recipe .epub
```

欢迎下载使用[我写的recipe][recipe]
[recipe]: https://github.com/RichardUSTC/calibre-recipes
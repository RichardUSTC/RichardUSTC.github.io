---
layout: post
title: "从CNBlog迁移到Github"
date: 2013-05-11 20:13
comments: true
categories: Other
---
我是从CNBlog迁移过来，所以需要将CNBlog的文章导入到新的博客中。

jekyll支持从多种别的博客平台迁移到当前平台，但是没有支持CNBlog，于是我不得不自己来完成这个博客导入的过程。

CNBlog的文章备份出来的是一个xml文件。我分析了这个文件之后，写了一个python脚本，完成从xml到markdown的转换。我的脚本号称转换成了markdown，其实只是增加了一个[yaml front matter][yaml]，内容部分仍然是原来的html。不过就效果来看，还马马虎虎可以接受，就不再继续折腾了。

脚本特点：

- 保留了原来的发表日期
- 保留了原来的标题，但是因为octopress对文件名为中文的文件支持不好，所以文件名改为了<date>-blog-<N>的形式
- 支持在转换的过程中指定分类
- 支持转换输出为html或者octopress使用的markdown格式

 这个python脚本已经上传的github上了，[这个是github链接][cnblog_extractor]，欢迎大家批评指正。

[yaml]: https://github.com/mojombo/jekyll/wiki/yaml-front-matter
[cnblog_extractor]: https://github.com/RichardUSTC/cnblogs-extractor

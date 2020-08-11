---
layout: post
title: "正则表达式与diff的结合"
date: 2013-06-09 14:12
comments: true
tags: Shell
---
diff是Linux下一个非常有用的工具，主要用来比较两个文件的不同。有时候，需要diff的两个文件中包含一些无关的信息，这些信息不同，导致diff的结果很复杂，难于分析。碰到这种情况，通常是利用sed之类的工具去掉源文件中的无关信息，生成临时文件，再对临时文件做diff。diff完成之后再删除临时文件。

今天刚好搜到StackOverflow上的一个问答，直接用diff的功能解决了这个问题，无需再创建和删除临时文件了。
```bash
diff --label example_file1 <(sed 's/example_old1_regex/example_new1_regex/' example_file1) \
	 --label example_file2 <(sed 's/example_old2_regex/example_new2_regex/' example_file2)
```
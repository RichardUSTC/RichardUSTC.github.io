---
layout: post
title: "Python笔记"
date: 2013-09-16 17:06
comments: true
categories: Python
published: true
---
这篇文章里将会总结Python中的各种小知识点。

### input和raw_input
使用方法：
```python
s = raw_input(tip)  # 'tip' is a tip
number = input(tip)
```
raw_input读入之后以字符串的形式存储，而input则会讲读入的字符串做一次求值，得到的值得类型跟字符串内容相同。
实际上，input是用raw_input来实现的。input(tip) -- eval(raw_input(tip))

### unpack
使用方法：
```python
first, second, third, forth = sys.argv  # There must be enough data for unpacking, otherwise exception will be raised
```

## Python常用标准库
### sys
sys.argv  
sys.exit([arg])  
sys.path  
sys.stdin  
sys.stdout  
sys.stderr
### os
os.system(command)  
os.environ
### 一些数据结构
<table border="1">
<tr><th>数据结构</th><th>所在包</th></tr>
<tr><td>set</td><td>默认</td></tr>
<tr><td>heap</td><td>heapq</td></tr>
<tr><td>deque</td><td>collections</td></tr>
</table>


### time
time.time()  
time.strftime(format)  
time.strptime(string[, format])  


### random
random.random()  
random.choice(seq)  
random.shuffle(seq[, random])

### shelve
shelve用于将内容存储到文件。
```python
import shelve
s = shelve.open("output.dat")
s['key'] = value
s.close()
```

### re
re.compile(pattern[, flags])  
re.search(pattern, string[, flags])  
re.match(pattern, string[, flags])  
re.split(pattern, string[, maxsplit=0])  
re.findall(pattern, string)  
re.sub(pattern, replace, string[, count=0])  
matchObj.group([groupNum1, ...])  
matchObj.start([groupNum])  
matchObj.end([groupNum])  
matchObj.span([groupNum])
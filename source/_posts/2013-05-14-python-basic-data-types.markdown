---
layout: post
title: "Python Basic Data Types"
date: 2013-05-14 21:21
comments: true
tags: Python
---

Python的基本数据结构主要包括Tuple、List、Dict、Set。

## Tuple
```python tuple.py
t = ()
t = tuple()
t = (1,2,3)
print t[0]
t[-1]    # equalvalent to t[len(t)-1]
```
Tuple中的元素一旦创建之后就不能改变，不能增加，不能减少，不能删除。

## List
```python list.py
l = []
l = list()
l = [1,2,3]
l.append(4)
l[5] = 5
del(l[0])
print l[0]
item = l.pop()
```
List中的元素创建之后还可以改变，可以增加，可以减少，可以删除。

## Dict
```python dict.py
d = {}
d = dict()
d = {"k1":"v1", "k2":"v2"}
d["k3"] = "v3"
print d["k1"]
print d.get("k1", "if k1 not found, return this value")
key, value = d.pop()
```
Dict用来保存key-value对。其中key必须是immutable的。

## Set
```python set.py
s = set()
s.add(2)
s.add(2) # After this operation, only one '2' is stored
item = s.pop() # return an arbitrary value of the set
```
Set中的元素是唯一的。

## Slice
Tuple和List支持slice。
```python slice.py
t = (0,1,2,3)
t[:]              # the whole tuple
t[0:2]            # t[0], t[1]
t[:2]             # same as t[0:2]
t[2:]             # same as t[2:len(t)-1]
t[-1:]            # reversed t

# The same goes for list
```
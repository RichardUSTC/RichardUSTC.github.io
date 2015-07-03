---
layout: post
title: "Python Module"
date: 2013-05-14 19:53
comments: true
categories: Python
---
Python中，module是用来组织代码的有效方法。

## 导入模块
```python import-module.py
import sys
from os import path
import xml.dom.minidom as minidom

print sys.argv[0]
path.exists("test.txt")
d = minidom.parse("test.xml")
```

## 判断模块代码是直接运行还是被导入
```python check-how-module-is-running.py
if __name__ == "__main__":
	print "module is running directly"
else:
	print "module is imported by another module"
```

## 模块搜索路径
导入模块的时候，python首先会在内置模块中查找，如果找不到，则会到sys.path中指定的路径中去找。sys.path包含这些内容：

 - 当前目录
 - PYTHONPATH PYTHONPATH类似于PATH，包含一系列的目录
 - 其他的一些跟安装相关的默认路径
当前目录的优先级比标准库的优先级更高。
Python程序在运行的过程中可以修改sys.path的值，从而影响模块的导入。

## 创建自己的模块
比如我自己写了一个模块，叫做mymoudle.py。

1. 如果在同一个目录下，直接`import mymodule`即可。
2. 如果相对路径为mymodule/mymodule.py，则需要在mymodule目录下创建`__init__.py`文件，然后`import mymodule.mymodule`。
3. 如果相对路径为mymodule/mymodule/mymodule.py，则需要在两个目录中都创建`__init__.py`文件，然后`import mymodule.mymodule.mymodule`。

## 其他
1. 使用`dir`可以查看module中所有可用的变量、类和函数等信息
2. 当模块被import之后，在模块py文件所在目录会生成`.pyc`文件。这个文件的内容和格式可参考[这篇文章][pyc]

[pyc]: http://blog.donews.com/lemur/archive/2006/02/21/736881.aspx

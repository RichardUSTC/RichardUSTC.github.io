title: python中的excepthook
date: 2015-07-09 08:20:17
tags: Python
---
Python中，可以通过sys.excepthook来实现对Python程序中的exception的自定义处理
```
import sys
import traceback

def exception_printer(exc_type, exc_obj, traceback_obj):
	print("exception type: %s" % type(exc_type))
	print("exception object: %r" % exc_obj)
	print("traceback content:\n%s" % '\n'.join(traceback.format_tb(traceback_obj)))

def test():
	sys.excepthook = exception_printer
	raise StandardError('test')

if __name__ == '__main__':
	test()

```
输出结果:
```
exception type: <type 'exceptions.StandardError'>
exception object: StandardError('test',)
traceback content:
  File "C:\Users\richard\Desktop\test.py", line 14, in <module>
    test()

  File "C:\Users\richard\Desktop\test.py", line 11, in test
    raise StandardError('test')
```

参考链接: [https://docs.python.org/2/library/sys.html](https://docs.python.org/2/library/sys.html)
---
layout: post
title: "Python Basics"
date: 2013-05-14 19:23
comments: true
categories: Python
---
最近在复习Python，顺便记录一下自己的复习笔记，以后可以再回来看看。

## Hello World

```python hello-world.py
if __name__ == "__main__":
	print "Hello, World."
```

## 分支
```python branch.py
if x==y:
	do_x_equal_y_stuff()
elif x==z:
	do_x_equal_z_stuff()
else:
    do_other_stuff()
```

## 循环
```python for-loop.py
for i in range(5):
	print i
	if i == j:
		break
	if i == k:
		continue
else:
	# do something if the loop end normally
	do_some_stuff()
```

```python while-loop.py
while x>0:
	x--
	print x
	if x == j:
		break
	if x == k:
		continue
else:
	#do something if the loop end normally
	do_some_stuff()
```

## 函数
```python simple-function.py
def add(a,b):
	''' return the result of a plus b''' # docstring, need to be at the first line of the function
	return a+b

#call the function
print add(1,3)
```

```python local-global.py
x=10        # Here, x is global
def foo():
	x = 50  # Here, x is local, does not affect the global x
def bar():
	global x
	x= 8    # Here, x is global
# test
foo()
print x
bar()
print x
```

```python default-parameters.py
def add(a, b, c=0, d=3):
	return a+b+c+d

#call the function
print add(1, 3)
print add(1, 3, 4)
print add(1, 3, d=2)
```

```python list-and-dict-parameters.py
def foo(a, *l, **d):
	print a
	for list_item in l:
		print list_item
	for key,value in d:
		print "{k}:{v}".format(k=key, v=value)

# test
foo(1, ["x","y"], {"key": "value"})
```

## 类
```python
__metaclass__ = type # new-style class
class ClassName(Parent1, Parent2):
	classVar = 0
	def __init__(self, parameter):
		super(ClassName, self).__init__(parameter)
		self.__class__.classVar += 1
		self.objectVar = 0
		pass
	def method(self, parameter):
		pass
	@classmethod
	def classMethod(cls, parameter):
		pass
	@staticmethod
	def staticMethod(parameter):
		pass
```

## 迭代器
```python
__metaclass__ = type
class Fib:
	def __init__(self):
		self.first = 1
		self.second = 1
	def __iter__(self):
		return self   # a iterator returns a object which implements the 'next' method
	def next(self):   # if there is no next item, raise 'StopIteration' exception
		ret = self.first
		tmp = self.second
		self.second = self.second + self.first
		self.first = tmp
		return ret
```

## 生成器
```python
def generator(l):
	for item in l:
		yield item
```

## 异常

```python
import exceptions #standard exceptions

def foo(parameters):
	try:
		# do something
		if someCondition :
			raise SomeError
		if someOtherCondition:
			raise someErrorObject
		# do something
	except SomeError:
		raise    # raise the exception again
	except (SomeError1, SomeError2): # catch two types of exceptions at the same site
		# do something
	except (SomeError3), errorObject:
		# do something with errorObject
	except:
		# catch all exceptions
	else:
		# do something if no exception is raised
	finally:
		# do something whether there is any exception or not
```
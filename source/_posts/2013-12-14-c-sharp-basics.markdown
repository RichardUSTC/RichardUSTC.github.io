---
layout: post
title: "C# Type Basic"
date: 2013-12-14 10:05
comments: true
categories: C#
---
C\#中所有的类型都有同一个基类object

## 类型种类
1. 值类型：简单类型、枚举类型、结构体类型、可空类型
2. 引用类型：类类型、接口类型、数组类型、委托类型
值类型可以通过boxing转换成引用类型，也可以从引用类型unboxing转换成值类型。转换过程中发生数据拷贝。

### 内置类型
sbyte/byte short/ushort int/uint long/ulong float double decimal bool char string  
注意：  
char类型跟C不一样，每一个char类型的变量可保存一个UTF-16字符，大小是2字节  
bool不能与整数类型相互转换  
decimal是用十进制存储的  
string类型是.NET Framework中String的别名，是一个引用类型，其他内置类型都是值类型。
```
short i = 0, j=1;
//short k = i+j; //error, need conversion
short k = (short)(i+j) //right

// if(i) doSomething(); //error, integer cannot be implicitly convert to boolean type
if(i != 0) doSomething(); //right

float f = 1.0F // F/f is essential. type of 1.0 is double
decimal d = 1.0M // M/m is essential. 
```

### class
C\#中class结构与Java的形式类似。  
class类型的变量必须在堆上申请空间。  
class是单继承的，但是可以实现多个接口。  
class类型是引用类型
```
public class Person
{
	private int age;
	private string name;
	public Person(int age, string name)
	{
		this.age = age;
		this.name = name;
	}
}
//inheritance
public class Student:Person
{
	private string studentNo;
	public Student(int age, string name, string studentNo) : base(age, name)
	{
		self.studentNo = studentNo;
	}
}
```

### struct
struct类型与类类型相似，但是不必在堆上申请空间，也不能被继承。  
struct类型不能有无参数构造函数，没有虚函数，没有finalizer。 
struct类型的构造函数必须初始化所有数据成员。   
struct类型是值类型。  

```  
public struct Point
{
	int x,y;
	public Point(int x, int y)
	{
		this.x = x;
		this.y = y;
	}
}
```

### interface
class类型和struct类型都可以实现interface。  
interface所有成员都是public并且是abstract的。  
interface只能包含方法成员。  
interface也可以扩展interface。  
```
public interface Phone
{
	void Call(string number);
	void SendSMS(string content);
}
public interface SmartPhone : Phone
{
	void InstallApp(string app);
}
public class Android: SmartPhone
{
	void Call(string number)
	{
		System.Console.WriteLine("calling " + number);
	}
	void SendSMS(string content)
	{
		System.Console.WriteLine("sending message: " + content);
	}
	void InstallApp(string app)
	{
		System.Console.WriteLine("installing app: " + app);
	}
}
```

###enum
enum默认底层用int实现。  
```
enum Direction{ East, West, North, South}
enum Direction2 : byte { East, West, North, South } //use byte as underlaying type
[Flags]
enum Direction3{ East=0, West=1, North=2, South=4}
enum Direction3 d = Direction3.East | Direction3.North;
System.Console.WriteLine(d); //will print "East, North"
```

### 可空类型
可空类型是C\#中比较特殊的类型。比如，int? a; int?是一种可空类型，a可以是一个int类型的值，也可以是null。  

### 委托类型
委托类型类似C语言中的函数指针，但是它是类型安全的，并且是面向对象的。
```
delegate double Function(double x); //defined a new type 'Function'
static double Square(double x){
	return x*x;
}
Function f = Square;
```
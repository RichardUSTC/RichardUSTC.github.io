---
layout: post
title: "javascript basic concept"
date: 2013-06-20 21:11
comments: true
tags: Javascript
---
以前也学过一段时间的Javascript，但是长时间不用，很多东西已经忘记了。趁此机会总结一下Javascript的基本用法。
## Javascript的位置
### head内
```javascript
<head>
	<script type="text/javascript">
		function hello(){
			alert("hello, world.");
		}
	</script> 
</head>
```
### body内
```javascript
<body>
	<script type="text/javascript">
		function hello(){
			alert("hello, world.");
		}
	</script> 
</body>
```
### 外部Javascript文件
<head>
	<script src="http://ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js"></script>
</head>

## Javascript的语句
Javascript的语句可以以';'结尾，也可以以换行来结尾。如果需要断行，可以使用'\'。
```javascript
alert("1");
alert("2")
alert("\
3")
```

## Javascript注释
```javascript
// comment in one line
/* comment
   in
   multiple
   line
*/
```

## Javascript变量
* Javascript中所有的变量都是对象。
* Javascript变量类型是动态可变的。
* 第一次初始化之前不声明，则变量是全局的。如果有声明，那么变量的作用域为变量声明所在的域。
* Javascript变量未初始化之前为undefined。
```javascript
var x;
x = 1;
x = "hello"
```

## Javascript中的数据类型
### 字符串
```javascript
var s1 = "hello";
var s2 = "world";
var s3 = x + y;
var s4 = "hello, 'world'";
```
### 数字
```javascript
var num1 = 1;
var num2 = 1.1;
var num3 = 1e10;
var num4 = 1e-3;
```
### 布尔值
```javascript
var b1 = true;
var b2 = false;
```
### 数组
```javascript
var arr = new Array();
arr[0] = 1;
arr[1] = "2";
var arr2 = new Array(1, "2");
var arr3 = [1, "2"];
arr.length
```
### 对象
```javascript
var obj1 = {f1:1, f2:"2"};
var i = obj1.f1;
var s = obj1.f2;
var obj2 = new Object();
obj2.f1 = 1;
obj2.f2 = "2";

function person(name, age){
	this.name = name;
	this.age = age;
}

sam = new person("Sam", "10");

```

## Javascript函数
```javascript
function func1(arg1, arg2){
	return arg1 + arg2;
}
func2 = function(arg1, arg2){
	return arg1 + arg2;
}
```

## Javascript运算符
1. +, -, *, /, %
2. ++, --
3. =, +=, -=, *=, /=, %=
4. ==, ===, !=, >, <, >=, <=
5. &&, ||, !

## Javascript语句

### 条件语句
与C语言类似

### switch语句
与C语言类似

### 循环语句

#### for循环
```javascript
for(var i=0; i<length; i++){
	arr[i] = i;
}
for(var i in x){
	arr[i] = 0;
}
```

#### while循环
与C语言类似

#### do..while循环
与C语言类似

### break语句
不带标签时与C语言类似
```javascript
list:{
	document.write(cars[0] + "<br>"); 
	document.write(cars[1] + "<br>"); 
	document.write(cars[2] + "<br>"); 
	break list;
	// The next three statement will not be executed
	document.write(cars[3] + "<br>"); 
	document.write(cars[4] + "<br>"); 
	document.write(cars[5] + "<br>"); 
}

var iNum = 0;

outermost:
for (var i=0; i<10; i++) {
  for (var j=0; j<10; j++) {
    if (i == 5 && j == 5) {
    break outermost;
  }
  iNum++;
  }
}

alert(iNum);	//输出 "55"
```
### continue语句
不带标签时与C语言类似
```javascript
var iNum = 0;

outermost:
for (var i=0; i<10; i++) {
  for (var j=0; j<10; j++) {
    if (i == 5 && j == 5) {
    	continue outermost;
  }
  iNum++;
  }
}
alert(iNum);	//输出 "95"
```

### try..catch
```javascript
try{
	//do something
	//throw some_err
	//do something
}catch(err){
	//deal with error
}
```

## Javascript操作DOM

### 查找
```javascript
var x=document.getElementById("intro");
var y=x.getElementsByTagName("p");
```

### 改变HTML
```javascript
document.write("new content") // originally contents will be flushed
document.getElementById("header").innerHTML="new header";
document.getElementById("image").src="landscape.jpg";
document.getElementById("p2").style.color="blue";
```

### 针对事件做出反应
```javascript
document.getElementById("myBtn").onclick=function(){displayDate()};
```
DOM[事件列表][dom-event]

### 创建新的HTML元素
```javascript
var para=document.createElement("p");
var node=document.createTextNode("这是新段落。");
para.appendChild(node);
```

### 删除HTML元素
```javascript
var parent=document.getElementById("div1");
var child=document.getElementById("p1");
parent.removeChild(child);
```

## Javascript Cheat Sheet
[Javascript Cheat Sheet](http://www.addedbytes.com/cheat-sheets/download/javascript-cheat-sheet-v1.png)

[dom-event]: http://www.w3school.com.cn/htmldom/dom_obj_event.asp
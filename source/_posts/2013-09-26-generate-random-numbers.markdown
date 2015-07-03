---
layout: post
title: "构造随机数生成器"
date: 2013-09-26 22:59
comments: true
categories: 
---

最近在准备找工作，碰到一些有意思的题目，其中一种就是利用现有的随机数生成器来构造新的随机数生成器。做了两道题之后，有一点体会，就此记录下来。
假设原来的随机数生成器每个可能值出现的概率为p，新的随机数生成器每个可能值出现的概率为q。当p和q的大小关系不同时，有不同的做法。

## p=q
这种情况下，新的随机数生成器是最好构造的，只需要将新的生成器的可能值与原有的生成器的可能值一一对应即可。

例如，已有r1是随机生成1-5的随机数生成器，那么随机生成7-11的随机数生成器r2=r1+6。更一般的情况可以使用switch-case结构来实现一一对应。
```c
//simple example
int r1();
int r2(){
	return r1()+6;
}

//general example
int r1();
int r2(){
	int result = r1();
	switch(result){
		case r1_v1:
			return r2_v1;
		...
		case r1_vn:
			return r2_vn;
	}
}
```

## p<q
这种情况也比较好处理。只需要将一部分现有随机数生成器的可能值与新的随机数生成器的可能值做一一对应即可。

例如，已有r1是随机生成1-5的随机数生成器，那么随机生成0-1的随机数生成器r2的实现如下。更一般的情况可以使用switch-case结构来实现。
```c
//simple example
int r1();
int r2(){
	int result;
	do{
		result = r1();
	}while(result<3);
	return result-1;
}

//general example
int r1();
int r2(){
	int result;
	do{
		result = r1();
		switch(result){
			case r1_v1:
				return r2_v1;
			...
			case r1_vn:
			    return r2_vn;
			default:
				//do nothing with other r2 possible values
		}
	}while(1);
}
```

## p>q
这种情况想对困难一些，也是经常被考察的一种情况。如果能够将情况转换成p\<q情况，那么就能很好的解决。看一个具体的实例。已有一个随机数生成器r1，随机生成0或1，现在需要构造一个新的随机生成器r2，使得r2能够随机生成1-5。

r2的可能值有6个，而r1只有两个。要用r1生成6个以上的值，可以通过加法的形式来完成。如果用两个r1相加，最多可以生成4个值，如果用3个r1相加，最多可以生成8个值，所以至少要3个r1相加。如果是r1+r1+r1，虽然生成了8个值，但是8个值中有相同的，导致每个值的概率是不一样的，不能用作随机数生成器。需要找到一个方法，让生成的8个值互不相同。考虑r3=r1+2r1+4r1，可能的生成值为0, 1, 2, 4, 3, 5, 6, 7。这8个值互不相同，每个出现的概率都是相同的。这个时候r3就是一个新的随机数生成器，并且符合p\<q的情况，可以用p\<q的解法来解决问题。

在更一般的情况下，构造r3 = a1\*r1+a2\*r1+...+am\*r1。为了生成足够的可能值，需要满足n_r1^m >= n_r2，其中n_r1和n_r2分别是r1和r2可能值的数目。在此基础上，还要让a1\*r1+a2\*r1+...+am\*r1不出现重复值。
```c
//simple example
int r1();
int r2(){
	int result;
	do{
		result = r1()+2*r1()+4*r2;
	}while(result<1 || result > 5);
	return result;
}

//general example
int r1();
int r2(){
	int result;
	do{
		result = a1*r1() + a2*r1() + ... + am*r1();
		switch(result){
			case v1:
				return r2_v1;
			...
			case vn:
			    return r2_vn;
			default:
				//do nothing
		}
	}while(1);
}

```
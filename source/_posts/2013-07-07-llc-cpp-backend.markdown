---
layout: post
title: "使用llc C++后端辅助LLVM IR编程"
date: 2013-07-07 15:23
comments: true
categories: LLVM
---
llc是LLVM提供的一个工具，以LLVM bitcode或LLVM汇编为输入，根据指定的后端生成相应的代码。通常情况下，后端跟某一种ISA相关，比如x86、mips、arm等，指定这些后端就会生成在相应处理器上运行的机器码。但是llc还支持一种特殊的后端：cpp。指定这种后端的时候，输出的是C++代码，而这些C++代码正好就是利用LLVM的IR API编程。所以可以利用这种方法来学习如何进行LLVM的IR编程。

##检查llc是否支持cpp后端
有时候发行版自带的llc可能并不支持cpp后端，比如ubuntu 13.04。查看llc支持的后端可以用以下命令
```bash
llc --version
```
如果输出中包含"cpp  - C++ backend"，那么说明支持C++后端了。

##编写源文件
这里给一个最简单的示例test.c
```c
int main(){return 0;}
```

##生成bitcode
生成LLVM的bitcode可以用clang，也可以用llvmgcc。

clang：
```bash
clang -emit-llvm -c test.c
```

llvmgcc：
```bash
llvmgcc -emit-llvm -c test.c
```
生成的LLVM bitcode代码文件后缀为.o

##生成LLVM汇编（可选）
可以从源文件生成LLVM汇编，也可以从bitcode生成汇编。

使用clang从源文件生成LLVM汇编：
```bash
clang -emit-llvm -S test.c
```
使用llvmgcc从源文件生成LLVM汇编：
```bash
llvmgcc -emit-llvm -S test.c
```
使用llvm-dis从bitcode生成LLVM汇编
```bash
llvm-dis test.o
```
生成的LLVM汇编内容：
```
; ModuleID = 'test.o'
target datalayout = "e-p:64:64:64-i1:8:8-i8:8:8-i16:16:16-i32:32:32-i64:64:64-f32:32:32-f64:64:64-v64:64:64-v128:128:128-a0:0:64-s0:64:64-f80:128:128-n8:16:32:64-S128"
target triple = "x86_64-unknown-linux-gnu"

; Function Attrs: nounwind uwtable
define i32 @main() #0 {
entry:
  %retval = alloca i32, align 4
  store i32 0, i32* %retval
  ret i32 0
}

attributes #0 = { nounwind uwtable "less-precise-fpmad"="false" "no-frame-pointer-elim"="true" "no-frame-pointer-elim-non-leaf"="true" "no-infs-fp-math"="false" "no-nans-fp-math"="false" "unsafe-fp-math"="false" "use-soft-float"="false" }
```

##生成C++代码
使用llc生成C++代码，输入可以是bitcode，也可以是LLVM汇编。
使用bitcode作为输入：
```bash
llc -march=cpp test.o
```
使用LLVM汇编作为输入，假设汇编文件为test.s：
```bash
llc -march=cpp test.s
```
会生成一个test.o.cpp或test.s.cpp文件，从这个文件可以看到如何利用LLVM的IR进行编程。

生成的代码有一百多行，在此就不贴出来了。

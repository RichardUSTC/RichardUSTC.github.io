---
layout: post
title: "在LLVM IR中绑定外部的变量和函数"
date: 2013-07-07 16:58
comments: true
categories: LLVM
---
[前面的文章][llvm-ir-programming]中提到了如何利用LLVM的IR进行编程，这篇文章将讲述如何将LLVM IR外部的变量和函数与LLVM IR结合起来。

## LLVM IR外部变量和外部函数
外部变量`value`和外部函数`foo`：
```cpp
int value = 10;

int foo(int x){
    return 2*x;
}
```

## 声明LLVM IR中的外部变量和函数
声明一个外部变量value，为int类型。
```cpp
LLVMContext & context = llvm::getGlobalContext();
GlobalVariable *v = cast<GlobalVariable>(module->getOrInsertGlobal("value", Type::getInt32Ty(context)));
```

声明一个外部函数foo，这个函数的原型为`int foo(int x)`
```cpp
Function *f = cast<Function>(module->getOrInsertFunction("foo", Type::getInt32Ty(context),
                                   Type::getInt32Ty(context), NULL));
```

## 构建一个LLVM IR表示的函数
接下来构建一个LLVM IR表示的函数bar。在bar中读入value的值，并作为foo的参数调用foo，bar再返回foo的返回值。
```cpp
//create a LLVM function 'bar'
Function* bar = cast<Function>(module->getOrInsertFunction("bar", Type::getInt32Ty(context),NULL));

//basic block construction
BasicBlock* entry = BasicBlock::Create(context, "entry", bar);
IRBuilder<> builder(entry);

//read 'value'
Value * v_IR = builder.CreateLoad(v);
//call foo(value)
Value * ret = builder.CreateCall(f, v_IR);
//return return value of 'foo'
builder.CreateRet(ret);
```

## 创建ExecutionEngine
```cpp
//create execution engine first
InitializeNativeTarget();
ExecutionEngine *ee = EngineBuilder(module).setEngineKind(EngineKind::JIT).create();
```

## 绑定LLVM IR外部的变量和函数
```cpp
//map global variable
ee->addGlobalMapping(v, &value);

//map global function
ee->addGlobalMapping(f, (void *)foo);
```

## JIT并运行
```cpp
void *barAddr = ee->getPointerToFunction(bar);
typedef int (*FuncType)();
FuncType barFunc = (FuncType)barAddr;

std::cout << barFunc() << std::endl;
```
运行结果是20，正好符合语义。

## 重新绑定LLVM IR外部变量或者外部函数
重新绑定LLVM IR外部变量或函数可以通过updateGlobalMapping来实现。
```cpp
ee->updateGlobalMapping(v, &value1);
ee->updateGlobalMapping(f, (void *)foo1);
```
不过重新绑定之后，需要重新JIT才可以将改变反应到生成代码上来。

本文示例地址：[llvm-ir-global-mapping](https://github.com/RichardUSTC/llvm-ir-global-mapping)

[llvm-ir-programming]: http://richardustc.github.io/blog/2013/06/programming-with-llvm-ir/

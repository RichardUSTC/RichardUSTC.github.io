---
layout: post
title: "使用LLVM IR编程"
date: 2013-06-19 16:40
comments: true
categories: LLVM
---
最近做的工作要使用LLVM IR来进行编程，借这篇文章来整理一下最近学到的一些东西。
LLVM是一个非常有名的编译器基础设施。访问[LLVM官方网站][llvm.org]获得更多信息。

## LLVM IR简介

LLVM有自己的一套中间表示IR(Intermedia Representation)。IR在编译器中承担着一个承前启后的角色。编译器前端对源程序进行语法和语义分析，生成IR。编译器后端则将IR汇编成对应的机器指令。此外，编译器中大部分的优化都是在IR上完成的。

LLVM的IR功能强大，其形式类似于RISC机器的指令。LLVM可以使用解释来执行IR，也可以利用JIT将IR翻译成对应的机器指令。

## LLVM IR相关概念
使用LLVM IR编程要涉及到Module, Function, BasicBlock, Instruction, ExecutionEngine等概念。下面对这些概念进行一个简单的说明。

### Module
可以将LLVM中的Module类比为C程序中的源文件。一个C源文件中包含函数和全局变量定义、外部函数和外部函数声明，一个Module中包含的内容也基本上如此，只不过C源文件中是源码来表示，Module中是用IR来表示。

### Function
Function是LLVM JIT操作的基本单位。Function被Module所包含。LLVM的Function包含函数名、函数的返回值和参数类型。Function内部则包含BasicBlock。

### BasicBlock
BasicBlock与编译技术中常见的基本块(basic block)的概念是一致的。BasicBlock必须以跳转指令结尾。

### Instruction
Instruction就是LLVM IR的最基本单位。Instruction被包含在BasicBlock中。

### ExecutionEngine
ExecutionEngine是用来运行IR的。运行IR有两种方式：解释运行和JIT生成机器码运行。相应的ExecutionEngine就有两种：Interpreter和JIT。ExecutionEngine的类型可以在创建ExecutionEngine时指定。

## LLVM IR编程基本流程
1. 创建一个Module
2. 在Module中添加Function
3. 在Function中添加BasicBlock
4. 在BasicBlock中添加指令
5. 创建一个ExecutionEngine
6. 使用ExecutionEngine来运行IR

## LLVM IR编程示例与说明
注意： LLVM处于快速发展之中，其API变化非常大，不同版本之间的API可能不兼容。本文的示例都基于LLVM 2.9。

### 创建Module
Module创建时需要一个context，通常使用global context。在例子中，Module的name被设置为`test`。
```c
    // Module Construction
    LLVMContext & context = llvm::getGlobalContext();
    Module* module = new Module("test", context);
```

### 在Module中添加Function
在Module中添加Function的方法比较多，这里介绍一种比较简洁的方法。下面的代码生成了一个函数`void foo(void)`。
```c
    Constant* c = module->getOrInsertFunction("foo",
    /*ret type*/                           Type::getVoidTy(context),
    /*args*/                               Type::getVoidTy(context),
    /*varargs terminated with null*/       NULL);
    Function* foo = cast<Function>(c); /* cast is provided by LLVM
    foo->setCallingConv(CallingConv::C);
```
到目前为止，还没有添加BasicBlock，函数foo仅仅是一个函数原型。第6行设置foo遵循C函数调用的规则。LLVM中的函数支持多种调用规则，通常使用C的调用规则即可。更多调用规则可以参考`llvm::CallingConv::ID`。

### 在Function中添加BasicBlock
创建BasicBlock可以使用BasicBlock类的静态函数Create。
```c
BasicBlock* block = BasicBlock::Create(context, "entry", foo);
```
第三个参数`foo`表示将`block`插入到Function `foo`中。

### 在BasicBlock中添加指令
下面介绍一个在BasicBlock中添加指令的简洁方法。这个方法使用了一个工厂类`IRBuilder`的实例`builder`。
首先，初始化`builder`。
```c
IRBuilder<> builder(block);
```
这里将`block`作为参数表示接下来的指令将被插入到`block`中。

接下来的一段代码开始向`block`中插入代码。含义包含在注释中。
```c
    //Create three constant integer x, y, z.
    Value *x = ConstantInt::get(Type::getInt32Ty(context), 3);
    Value *y = ConstantInt::get(Type::getInt32Ty(context), 2);
    Value *z = ConstantInt::get(Type::getInt32Ty(context), 1);
    
    //addr = &value
    /* we will check the value of 'value' and see
    ** whether the function we construct is running correctly.
    */
    long value = 10;
    Value * addr = builder.CreateIntToPtr(
        ConstantInt::get(Type::getInt64Ty(context), (uint64_t)&value),
        Type::getInt64PtrTy(context),
        "addr"
    );

    // mem = [addr]
    Value* mem = builder.CreateLoad(addr, "mem");
    // tmp = 3*mem
    Value* tmp = builder.CreateBinOp(Instruction::Mul,
                                     x, mem, "tmp");
    // tmp2 = tmp+2
    Value* tmp2 = builder.CreateBinOp(Instruction::Add,
                                      tmp, y, "tmp2");
    // tmp3 = tmp2-1
    Value* tmp3 = builder.CreateBinOp(Instruction::Sub,
                                      tmp2, z, "tmp3");
    // [addr] = mem
    builder.CreateStore(tmp3, addr); 
    // ret
    builder.CreateRetVoid();
```
至此，我们通过LLVM的IR生成一个Module `test`，这个Module中包含一个Function `foo`，而`foo`中包含一个BasicBlock `entry`。
### 展示已经生成的IR
我们可以使用Module的dump方法先展示目前的成果。
```c
    module->dump();
```
输出结果
```
; ModuleID = 'test'

define void @foo(void) {
entry:
  ; the number '140735314124408' maybe different on your machine.
  %mem = load i64* inttoptr (i64 140735314124408 to i64*) 
  %tmp = mul i32 3, i64 %mem
  %tmp2 = add i32 %tmp, 2
  %tmp3 = sub i32 %tmp2, 1
  ; the number '140735314124408' maybe different on your machine.
  store i32 %tmp3, i64* inttoptr (i64 140735314124408 to i64*)
  ret void
}
```

### 创建ExecutionEngine
接下来就要使用ExecutionEngine来生成代码了。

创建一个JIT类型的ExecutionEngine，为了便于观察IR生成的机器码，设置为不优化。
```c
InitializeNativeTarget();
    ExecutionEngine *ee = EngineBuilder(module).setEngineKind(EngineKind::JIT)
        .setOptLevel(CodeGenOpt::None).create();
```

### 生成机器指令
JIT生成机器指令以Function为单位。
```c
    void * fooAddr = ee->getPointerToFunction(foo);
    std::cout <<"address of function 'foo': " << std::hex << fooAddr << std::endl;
```
如果用gdb跟踪函数执行，待输出fooAddr后，用`x/i`命令，即可查看`foo`对应的机器指令。
例如，我的X86_64机器上输出为：
```
   0x7ffff7f6d010:  movabs $0x7fffffffe2b0,%rax
   0x7ffff7f6d01a:  mov    $0x3,%ecx
   0x7ffff7f6d01f:  mov    (%rax),%edx
   0x7ffff7f6d021:  imul   %ecx,%edx
   0x7ffff7f6d024:  add    $0x2,%edx
   0x7ffff7f6d02a:  sub    $0x1,%edx
   0x7ffff7f6d030:  mov    %edx,(%rax)
   0x7ffff7f6d032:  retq
```

### 运行机器指令
使用类型转换将fooAddr转换成一个函数fooFunc，然后调用。
```c
    //Run the function
    std::cout << std::dec << "Before calling foo: value = " << value <<  std::endl;
    typedef void (*FuncType)(void);
    FuncType fooFunc = (FuncType)fooAddr;
    fooFunc();
    std::cout << "After calling foo: value = " << value <<  std::endl;
```
我们使用value的值来检验`foo`构造的正确性。运行之后的输出
```
Before calling foo: value = 10
After calling foo: value = 31
```
经过验算，`foo`的功能是正确的。

### 直接生成并运行机器指令
ExecutionEngine还提供一个接口`runFunction`直接JIT并运行机器指令。具体做法可以参考`LLVM::ExecutionEngine::runFunction`的文档。

## 代码
本文中的全部代码可以在[这里查看][code]。

[llvm.org]: http://www.llvm.org/
[code]: https://github.com/RichardUSTC/llvm-ir-programming-example

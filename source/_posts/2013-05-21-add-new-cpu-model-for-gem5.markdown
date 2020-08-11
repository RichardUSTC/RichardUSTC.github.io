---
layout: post
title: "Add a New CPU Model for Gem5"
date: 2013-05-21 12:10
comments: true
tags: Other
---
## Introduction
[Gem5][gem5] is a simulator system, which can support several kinds of ISA and several kinds of CPU.

The documentation of ["Adding a New CPU Model"][adding-new-cpu] is out of date. I tried to update the documentation, but the update has not been approved yet. So, I decide to write down the procedures in my own blog. Some of the procedures are based the old documentation.

## Overview ##
First, make sure you have basic understanding of how the CPU models function within the M5 framework. A good start is the [CPU Models][cpu-model] page.

This brief tutorial will show you how to create a custom CPU model called 'MyCPU', which will just be a renamed version of the AtomicSimpleCPU. After you learn how to compile and build 'MyCPU', then you have the liberty to edit the 'MyCPU' code at your heart's content without worrying about breaking any existing M5 CPU Models.

## Port C++ Code for MyCPU ##
The easiest way is to derive a new C++ class of your CPU Model from M5 CPU Models that are already defined and the easiest model to start with is the 'AtomicSimpleCPU' located in the 'gem5/src/cpu/simple' directory. 


For this example, we'll just copy the files from the 'gem5/src/cpu/simple' and place them in our own CPU directory: gem5/src/cpu/mycpu.
```bash
me@mymachine:~/gem5$ cd src/cpu
me@mymachine:~/gem5/src/cpu$ cp -r simple mycpu
```


Now check the mycpu directory to make sure you've copied the files successfully.
```bash
me@mymachine:~/gem5/src/cpu$ cd mycpu 
me@mymachine:~/gem5/src/cpu/mycpu$ ls
AtomicSimpleCPU.py  BaseSimpleCPU.py  SConscript  SConsopts  TimingSimpleCPU.py  
atomic.cc  atomic.hh  base.cc  base.hh timing.cc  timing.hh
```



Let's remove the files TimingSimpleCPU.py, timing.cc and timing.hh. We don't need them.
```bash
me@mymachine:~/gem5/src/cpu/mycpu$ rm TimingSimpleCPU.py timing.cc timing.hh
me@mymachine:~/gem5/src/cpu/mycpu$ ls
BaseSimpleCPU.py AtomicSimpleCPU.py  SConscript  SConsopts  atomic.cc  atomic.hh  base.cc  base.hh
```

Since we want to change 'AtomicSimpleCPU' to 'MyCPU', we will just replace all the names in the AtomicSimpleCPU.py, atomic.\* files and name them MyCPU.py, mycpu.\* files. We don't need AtomicSimpleCPU.py, atomic.cc and atomic.hh any more, so we will remove them.
```bash
me@mymachine:~/gem5/src/cpu/mycpu$ for file in *; do perl -pe s/AtomicSimpleCPU/MyCPU/g $file > $file.tmp; mv $file.tmp $file; done
me@mymachine:~/gem5/src/cpu/mycpu$ mv AtomicSimpleCPU.py MyCPU.py
me@mymachine:~/gem5/src/cpu/mycpu$ mv atomic.cc mycpu.cc
me@mymachine:~/gem5/src/cpu/mycpu$ mv atomic.hh mycpu.hh
me@mymachine:~/gem5/src/cpu/mycpu$ ls
BaseSimpleCPU.py MyCPU.py SConscript SConsopts base.cc base.hh mycpu.hh mycpu.cc
```

We also need to change 'BaseSimpleCPU' to 'BaseMyCPU', or we will have duplicated definition of 'BaseSimpleCPU' when compiling.
```bash
me@mymachine:~/gem5/src/cpu/mycpu$ for file in *; do perl -pe s/BaseSimpleCPU/BaseMyCPU/g $file > $file.tmp; mv $file.tmp $file; done
me@mymachine:~/gem5/src/cpu/mycpu$ mv BaseSimpleCPU.py BaseMyCPU.py
```

Next, you need to edit all your files to only include files in 'mycpu' directory instead of 'simple' directory. We also need to replace 'atomic.hh' to 'mycpu.hh' in mycpu.cc file.
```bash
me@mymachine:~/gem5/src/cpu/mycpu$ for file in *; do perl -pe s/atomic\.hh/mycpu\.hh/g $file >$file.tmp; mv $file.tmp $file; done
me@mymachine:~/gem5/src/cpu/mycpu$ for file in *; do perl -pe s#/simple/#/mycpu/#g $file > $file.tmp; mv $file.tmp $file; done
```

Next, you need to comment out the function 'change_thread_state' from base.cc, or there will be duplicated definition of 'change_thread_state' when compiling.
```bash
me@mymachine:~/gem5/src/cpu/mycpu$ vim base.cc
```

NOTE: The AtomicSimpleCPU is really just based off the BaseSimpleCPU (src/cpu/simple/base.hh) so your new CPU Model MyCPU is really a derivation off of this CPU model. Additionally, the BaseSimpleCPU model is derived from the BaseCPU (src/cpu/base.hh). As you can see, M5 is heavily object oriented.

## Making M5 Recognize MyCPU ##
Now that you've created a separate directory and files for your MyCPU code (i.e. gem5/src/cpu/mycpu), there are a couple files that need to be updated so that M5 can recognize MyCPU as a build option.

`gem5/src/cpu/mycpu/SConscript`: Edit the SConscript for your CPU model and add the relevant files that need to be built in here. Your file should only contain this code.
```python SConscript
Import('*')

if 'MyCPU' in env['CPU_MODELS']:
    SimObject('MyCPU.py')
    Source('mycpu.cc')
    DebugFlag('MyCPU')
    Source('base.cc')
    SimObject('BaseMyCPU.py')
```

`gem5/src/cpu/mycpu/SConsopts`: Edit the SConscript for your CPU model and add in CPU Model-specific information for the ISA Parser. The ISA Parser will use this when referring to the "Execution Context" for executing instructions. For instance, the AtomicSimpleCPU's instructions get all of their information from the actual CPU (since it's a 1 CPI machine). Thus, instructions only need to know the current state or "Execution Context" of the 'AtomicSimpleCPU' object. However, the instructions in a O3CPU needed to know the register values (& other state) only known to that current instruction so it's "Execution Context" is the O3DynInst object. (check out the [ISA Description Language documentation][isa-description-language] page for more details). Your file should only contain this code:
```python SConsopts

Import('*')

CpuModel('MyCPU', 'my_cpu_exec.cc',
         '#include "cpu/mycpu/mycpu.hh"',
         { 'CPU_exec_context': 'MyCPU' },
         default=True)
```

`gem5/src/cpu/static_inst.hh`: Put a forward class declaration of your model in here
```cpp static_inst.hh
...
class CheckerCPU;
class FastCPU;
class AtomicSimpleCPU;
class TimingSimpleCPU;
class InorderCPU;
class MyCPU;
...
```
You alse need to add 'MyCPU' to 'CPU_MODELS' so that it will be compiled latter. Navigate to the 'build_opts' directory.
```bash
me@mymachine:~/gem5/src/python/objects$ cd ~/gem5/build_opts
me@mymachine:~/gem5/build_opts$ for file in *;do perl -pe s/AtomicSimpleCPU/AtomicSimpleCPU,MyCPU/g $file > $file.tmp; mv $file.tmp $file; done
```

## Building MyCPU ##
Before building your cpu model, you need to make sure you have all the needed tools installed. See [Dependencies][dependencies] page for details.
Navigate to the M5 top-level directory and build your model:
```bash
me@mymachine:~/gem5/src/python/objects$ cd ~/gem5
me@mymachine:~/gem5$ scons build/MIPS/gem5.debug
scons: Reading SConscript files ...
Checking for leading underscore in global variables...(cached) no
Checking for C header file Python.h... (cached) yes
Checking for C library pthread... (cached) yes
Checking for C library dl... (cached) yes
Checking for C library util... (cached) yes
Checking for C library m... (cached) yes
Checking for C library python2.7... (cached) yes
Checking for accept(0,0,0) in C++ library None... (cached) yes
Checking for zlibVersion() in C++ library z... (cached) yes
Checking for clock_nanosleep(0,0,NULL,NULL) in C library None... (cached) no
...
 [  RANLIB]  -> libelf/libelf.a
 [   SHCXX] gzstream/gzstream.cc -> .os
 [      AR]  -> gzstream/libgzstream.a
 [  RANLIB]  -> gzstream/libgzstream.a
 [    LINK]  -> MIPS/gem5.debug
scons: done building targets.
```

If you are compiling on a dual-core CPU, use this command-line to speed-up the compilation:
```bash
scons -j2 build/MIPS/gem5.debug
```


## Creating Configuration Scripts for MyCPU ##
Now that you have a M5 binary built for use with the MIPS Architecture in a M5 MyCPU Model, you are almost ready to simulate. Note that the standard M5 command line requires that you provide at least a configuration script for the M5 binary to use. 

The easiest way to get up and running is to use the sample Syscall-Emulation script: `configs/example/se.py`.

You'll note that line 9 of the se.py Python script imports the details of what type of Simulation will be run (e.g. what CPU Model?) from the Simulation.py file found here:
gem5/configs/common/Simulation.py. And then, the Simulation.py file imports it's CPU Model options from the Options.py file in the same directory. Edit those two files and your M5 binary will be ready to simulate.

`gem5/configs/common/Options.py`: Add 'mycpu' to the cpu_type list...
```python Options.py
...
    parser.add_option("--cpu-type", type="choice", default="atomic",
                      choices = ["atomic", "timing", "mycpu", "detailed", "inorder",
                                 "arm_detailed"],
                      help = "type of cpu to run with")
...
```


`gem5/configs/common/Simulation.py`: Edit the Simulation script to recognize your model as a CPU class. After your edits, the setCPUClass function should look like this:
```python Simulation.py
...
def setCPUClass(options):
...
    elif options.cpu_type == "inorder":
        if not options.caches:
            print "InOrder CPU must be used with caches"
            sys.exit(1)
        class TmpClass(InOrderCPU): pass
    elif options.cpu_type == "mycpu":
        class TmpClass(MyCPU):pass
        atomic = True
    else:
        class TmpClass(AtomicSimpleCPU): pass
        atomic = True
...
```

## Testing MyCPU ##
Once you've edited the configuration scripts, you can run a M5 simulation from the top level directory with this command line:
```bash
me@mymachine:~/gem5$ ./build/MIPS/gem5.debug configs/example/se.py --cpu-type mycpu --cmd tests/test-progs/hello/bin/mips/linux/hello
gem5 Simulator System.  http://gem5.org
gem5 is copyrighted software; use the --copyright option for details.

gem5 compiled May 17 2013 20:17:23
gem5 started May 17 2013 20:22:42
gem5 executing on server
command line: ./build/MIPS/gem5.debug ./configs/example/se.py --cpu-type mycpu --cmd tests/test-progs/hello/bin/mips/linux/hello
Global frequency set at 1000000000000 ticks per second
warn: CoherentBus system.membus has no snooping ports attached!
0: system.remote_gdb.listener: listening for remote gdb #0 on port 7000
**** REAL SIMULATION ****
info: Entering event queue @ 0.  Starting simulation...
info: Increasing stack size by one page.
Hello World!
hack: be nice to actually delete the event here
Exiting @ tick 2913500 because target called exit()
```

NOTE: This binary path refers to the gem5/tests directory.

## Summary ##
So the above "tutorial" showed you how to build your own CPU model in M5. Now, it's up to you to customize the CPU Model as you like and do your experiments! Good luck!


[gem5]: http://www.m5sim.org/Main_Page
[adding-new-cpu]: http://www.m5sim.org/Adding_a_New_CPU_Model
[cpu-model]: http://www.m5sim.org/CPU_Models
[isa-description-language]: http://www.m5sim.org/The_M5_ISA_description_language
[dependencies]: http://www.m5sim.org/Dependencies
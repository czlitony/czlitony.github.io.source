---
title: SCONS Introduction
categories : scons
tags : [scons]
date : 2018-02-27 08:47:00
---

Breifly introduce SCONS.

<!-- more -->

# SConstruct
SConstruct file is a python script. The SConstruct file is the input file that SCons reads to control the build.
Example:
```
.
├── SConstruct
└── hello.cpp
```
hello.cpp:
```
#include <iostream>
using namespace std;

int main() {
    cout << "Hello World!" << endl;
    return 0;
}
```
SConstruct:
```
Program('hello.cpp')
```
Now we can build the program.
```
➜  /Users/chengzhI/scons/SConstruct > scons
scons: Reading SConscript files ...
scons: done reading SConscript files.
scons: Building targets ...
g++ -o hello.o -c hello.cpp
g++ -o hello hello.o
scons: done building targets.
```
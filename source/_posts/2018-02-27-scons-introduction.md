---
title: SCONS Introduction
categories : scons
tags : [scons]
date : 2018-02-27 08:47:00
---

Breifly introduce SCONS.

<!-- more -->
References:  
[SCONS User Manual](http://scons.org/doc/production/HTML/scons-man.html)  
[SCONS User Guide](http://scons.org/doc/production/HTML/scons-user/index.html)  
[SCONS API](http://scons.org/doc/latest/HTML/scons-api/index.html)

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
# SConscript
As we've already seen, the build script at the top of the tree is called SConstruct. The top-level SConstruct file can use the SConscript function to include other subsidiary scripts in the build. These subsidiary scripts can, in turn,use the SConscript function to include still other scripts in the build.
For example, a top-level SConstruct file might arrange for four subsidiary scripts to be included in the build as follows:
```
SConscript(['drivers/display/SConscript',
            'drivers/mouse/SConscript',
            'parser/SConscript',
            'utilities/SConscript'])
```
In this case, the SConstruct file lists all of the SConscript files in the build explicitly. (Note, however, that not every directory in the tree necessarily has an SConscript file.) Alternatively, the drivers subdirectory might contain an intermediate SConscript file, in which case the SConscript call in the top-level SConstruct file would look like:
```
SConscript(['drivers/SConscript',
            'parser/SConscript',
            'utilities/SConscript'])
```
And the subsidiary SConscript file in the drivers subdirectory would look like:
```
SConscript(['display/SConscript',
            'mouse/SConscript'])
```
# Build Targets
scons is normally executed in a top-level directory containing a SConstruct file, optionally specifying as command-line arguments the target file or files to be built.
By default, the command
```
scons
```
will build all target files in or below the current directory. Explicit default targets (to be built when no targets are specified on the command line) may be defined the SConscript file(s) using the Default() function, described below.  
Even when Default() targets are specified in the SConscript file(s), all target files in or below the current directory may be built by explicitly specifying the current directory (.) as a command-line target:
```
scons .
```
Building all target files, including any files outside of the current directory, may be specified by supplying a command-line target of the root directory (on POSIX systems):
```
scons /
```
or the path name(s) of the volume(s) in which all the targets should be built (on Windows systems):
```
scons C:\ D:\
```
To build only specific targets, supply them as command-line arguments:
```
scons foo bar
```
in which case only the specified targets will be built (along with any derived files on which they depend).
# Default
This specifies a list of default targets, which will be built by scons if no explicit targets are given on the command line. Multiple calls to Default are legal, and add to the list of default targets.
Multiple targets should be specified as separate arguments to the Default method, or as a list. Default will also accept the Node returned by any of a construction environment's builder methods.
Examples:
```
Default('foo', 'bar', 'baz')
env.Default(['a', 'b', 'c'])
hello = env.Program('hello', 'hello.c')
env.Default(hello)
```
An argument to Default of None will clear all default targets. Later calls to Default will add to the (now empty) default-target list like normal.
The current list of targets added using the Default function or method is available in the DEFAULT_TARGETS list; see below.
# Alias
Creates one or more phony targets that expand to one or more other targets.
Examples:
```
env = Environment()
hello = env.Program('hello.cpp')
env.Install('/usr/local/bin', hello)
Alias('install', '/usr/local/bin')
```
# Command
Executes a specific action (or list of actions) to build a target file or files.
Examples:
```
Command("file.out", "file.in", Copy("$TARGET", "$SOURCE"))


def builderMethod(target, source, env):
    # create targets by processing model
    targets, sources = processModel(env)
builder = builderEnv.Command(targets, sources, builderMethod)
```
# Tool
The Tool form of the function returns a callable object that can be used to initialize a construction environment using the tools keyword of the Environment() method.
[SCONS ToolsIndex](https://bitbucket.org/scons/scons/wiki/ToolsIndex)
Tools in Jabber:
```
env.Tool(shoggoth.tool)
env.Tool(msvcgen.tool)
```
/Users/chengzhI/jabber/trunk/tools/scripts/build/csfenv.py:
```
DEFAULT_TOOLS = [
    'libraries',
    'platforms',
    'emitters',
    'builddir',
    csftest.tool,
    output.tool,
    preCommit.tool,
    iniInstallAndAppend.tool,
    reflection.tool
]

def Environment(**kw):
    kwargs, tools = environmentKwargs(kw, DEFAULT_TOOLS)
    ...
    # Load default tools and tools specified by the caller
    for tool in tools:
        env.Tool(tool, **kwargs)
    
```
# Emitter
A function or list of functions to manipulate the target and source lists before dependencies are established and the target(s) are actually built.
Example:
```
import os
env = Environment()

def Output_Emitter(target, source, env):
    out_targets = []
    for t in target:
        out_target = File(os.path.join('out', str(t)))
        out_targets.append(out_target)

    return out_targets, source

env['LIBEMITTER'] = Output_Emitter

env.StaticLibrary('hello', 'hello.cpp')
```
Jabber uses Emitter to control the build output directory.
/Users/chengzhI/jabber/trunk/tools/scripts/build/csfenv.py
```
DEFAULT_TOOLS = [
    'libraries',
    'platforms',
    'emitters',
    'builddir',
    csftest.tool,
    output.tool,
    preCommit.tool,
    iniInstallAndAppend.tool,
    reflection.tool
]
```
/Users/chengzhI/jabber/trunk/tools/scripts/build/builddir.py
```
DEFAULT_LOCATION = {
    'bin': 'bin',
    'staticlib': 'bin',
    'staticservicelib': 'bin',
    'sharedlib': 'bin',
    'sharedservicelib': 'bin',
    'obj-static': 'obj-static',
    'obj-shared': 'obj-shared'
}

def generate(env, **kwargs):
    ...
    Setup_Emitters(env)
```

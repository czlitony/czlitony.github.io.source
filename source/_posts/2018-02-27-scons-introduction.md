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

# SCONS basic knowledge
## SConstruct
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
## SConscript
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
## Build Targets
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
## Build Types
### Object
Build object files.
```
Object('hello.c')
```
Now when you run the scons command to build the program, it will build just the hello.o object file on a POSIX system:
```
% scons
scons: Reading SConscript files ...
scons: done reading SConscript files.
scons: Building targets ...
cc -o hello.o -c hello.c
scons: done building targets.
```
See also StaticObject, SharedObject.
### Library, StaticLibrary
Build static libraries.
```
Library('foo', ['f1.c', 'f2.o', 'f3.c', 'f4.o'])

StaticLibrary('foo', ['f1.c', 'f2.c', 'f3.c'])
```
### SharedLibrary
Build shared (DLL) libraries.
```
SharedLibrary('foo', ['f1.c', 'f2.c', 'f3.c'])
```
### LoadableModule
On most systems, this is the same as SharedLibrary. On Mac OS X (Darwin) platforms, this creates a loadable module bundle.
```
module = envLocal.LoadableModule('CiscoSparkMercury', sources)
```
### Program
Build programs.
```
Program('bar.c')
```
## Environment
An environment is a collection of values that can affect how a program executes. SCons distinguishes between three different types of environments that can affect the behavior of SCons itself (subject to the configuration in the SConscript files), as well as the compilers and other tools it executes:
### External Environment
The external environment is the set of variables in the user's environment at the time the user runs SCons. These variables are available within the SConscript files through the Python ***os.environ*** dictionary.
### Construction Environment
A construction environment is a distinct object creating within a SConscript file and and which contains values that affect how SCons decides what action to use to build a target, and even to define which targets should be built from which sources. One of the most powerful features of SCons is the ability to create multiple construction environments, including the ability to clone a new, customized construction environment from an existing construction environment.
### Execution Environment
An execution environment is the values that SCons sets when executing an external command (such as a compiler or linker) to build one or more targets. Note that this is not the same as the external environment (see above).
***The default value of the PATH environment variable on a POSIX system is /usr/local/bin:/bin:/usr/bin. The default value of the PATH environment variable on a Windows system comes from the Windows registry value for the command interpreter.*** If you want to execute any commands--compilers, linkers, etc.--that are not in these default locations, you need to set the PATH value in the $ENV dictionary in your construction environment.

The simplest way to do this is to initialize explicitly the value when you create the construction environment; this is one way to do that:
```
path = ['/usr/local/bin', '/bin', '/usr/bin']
env = Environment(ENV = {'PATH' : path})
```
## Default
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
## Alias
Creates one or more phony targets that expand to one or more other targets.
Examples:
```
env = Environment()
hello = env.Program('hello.cpp')
env.Install('/usr/local/bin', hello)
Alias('install', '/usr/local/bin')
```
## Command
Executes a specific action (or list of actions) to build a target file or files.
Examples:
```
Command("file.out", "file.in", Copy("$TARGET", "$SOURCE"))


def builderMethod(target, source, env):
    ## create targets by processing model
    targets, sources = processModel(env)
builder = builderEnv.Command(targets, sources, builderMethod)
```
## Tool
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
    ## Load default tools and tools specified by the caller
    for tool in tools:
        env.Tool(tool, **kwargs)
    
```
## Emitter
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
Jabber uses Emitter to control the build output directory: tools/scripts/build/csfenv.py

## PCH
[Microsoft Pre-compiled headers technology](https://blog.csdn.net/luoweifu/article/details/49010627)
Builds a Microsoft Visual C++ precompiled header. Calling this builder method returns a list of two targets: the PCH as the first element, and the object file as the second element. Normally the object file is ignored. This builder method is only provided when Microsoft Visual C++ is being used as the compiler. The PCH builder method is generally used in conjunction with the PCH construction variable to force object files to use the precompiled header.
The useage of PCH in Jabber can refer to tools/scripts/build/csfenv.py.

# Jabber build system introduction
## shoggoth
A shoggoth (occasionally shaggoth[1]) is a monster in the Cthulhu Mythos. Here shoggoth in Jabber is a tool to generate API header files, base class files, Object C wrappers and Jets reflactors files.
e.g.
```
ShoggothCPPInterface: Generate Interface headers
ShoggothCPPNotifierImpl: Generate Notifier Implement files
ShoggothCPPBoilerplate: Generate Base Implement files
ShoggothReflectorHeader: Generate Jets sreflactor headers
ShoggothReflectorBody: Generate Jets reflactor implement files
```

shoggoth python script file lation:
tools\jcfscripts\shoggoth\shoggoth.py
## csfenv.Environments
Create SCONS Construction Environments to build Jabber.
There are some major jobs done when contruct the Environment:
### Inject some default tools
Those are the default tools:
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
In those tools, the function of 'emitters' is to put the output targets in specific folders.
e.g. On Windows, dynamic libraries and executive programs etc. are put in the folder 'products\jabber-win\src\jabber-client\jabber-build\Win32\bin\Release'
### Add the library "builders" to the environment
```
# Add the Service library Builders to the environment
env.AddMethod(StaticServiceLibrary)
env.AddMethod(SharedServiceLibrary)
# Add the Prebuilt library "builders" to the environment
env.AddMethod(PrebuiltSharedLibrary)
env.AddMethod(PrebuiltStaticLibrary)
```

### Builds a Microsoft Visual C++ precompiled header.
Those are the precompiled files:
```
pre-compiled.h
pre-compiled-debug.cpp
pre-compiled-release.cpp
```
On Windows, the precompiled header is generated by default.
If we want to build '*.c' sources, we can remove the precompiled header by this way:
```
envLocal = env.Clone()

if envLocal.get('PCH', None) != None:
    envLocal.no_pre_compiled_headers()
```

### Inject some variables
```
for env in csfenv.Environments(BUILD_TARGETS,
                               CSF_PROJECT_NAME='jabberwerxcpp',
                               windows_arch='x86',
                               windows_mbcs='unicode',
                               macosx_arch='x86_64'):
```

windows_arch: Windows Jabber architecture
macosx_arch: Mac Jabber architecture
windows_mbcs: [Unicode and MBCS - MSDN - Microsoft](https://msdn.microsoft.com/en-us/library/cwe8bzh0.aspx). If you see this error  'error C2440: cannot convert from 'const char [7]' to 'LPCWSTR'', you need to set windows_mbcs to 'mbcs'. 
CUSTOM_BUILDER_OUTPUT: Custom the build output directory, replace the default emitter output directory.

csfenv.Environments python script file lation:
tools\scripts\build\csfenv.py
## RegisterLibrary and UseLibrary
### RegisterLibrary
```
env.RegisterLibrary('systemservice',   (Note that here uses 'env' instead of a cloned environment)
    COMPILE={
        'CPPPATH': [Dir('../include').abspath,Dir('../api').abspath]
    },
    LINK={
        'LIBS': linkLibraries,
        'LINKFLAGS': linkflags
    },
    GET_SERVICE_FILES = {
        'SERVICE_FILES' : serviceFiles
    })
```

### UseLibrary
```
envLocal.UseLibrary('jcfcoreutils', actions=['COMPILE'])
envLocal.UseLibrary('csfstorage', actions=['COMPILE'])
envLocal.UseLibrary('systemmonitor', actions=['COMPILE'])
envLocal.UseLibrary('servicesframework')
envLocal.UseLibrary('configservice')
envLocal.UseLibrary('csfnetutils')
```
RegisterLibrary and UseLibrary python script file lation:
tools\scripts\build\libraries.py
## Use thirdparty pre-buildt libraries
PrebuiltSharedLibrary
PrebuiltStaticLibrary
e.g.
```
if envLocal.TargetPlatform() == 'windows':
    includePath = [os.path.join(rootDir, 'include'), os.path.join(rootDir, 'windows', env['msvs'], 'include')]
    libsPath = os.path.join(rootDir, 'windows', env['msvs'], configuration, 'lib')
    built_files = Glob(os.path.join(libsPath,'*.dll')) + Glob(os.path.join(libsPath,'*.lib'))
    dllFiles, libFiles = envLocal.PrebuiltSharedLibrary(built_files)
    
    libFiles.append('ws2_32.lib')

env.RegisterLibrary('libcurl',
    COMPILE={
        'CPPPATH': includePath 
     },
    LINK={
        'LIBS': libFiles
    },
    GET_DLLS = {
        'DLLS': dllFiles
    })
```

## dependencyList.txt
This file define all the submodules in a project.
Those submodules may rely on each other, those sunmodles are sequential. e.g. If A relies on B, then B should be put before A;
```
servicesframework
configservice-api
configservice (servicesframework is relied by configservice, so servicesframework should be put before configservice )
```
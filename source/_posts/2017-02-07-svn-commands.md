---
title: SVN常用命令
categories : SVN
tags : [svn commands]
date : 2017-02-07 17:02:30
---

介绍一些常用的svn命令。

<!-- more -->

# 查看修改的文件记录
svn cat -- 显示特定版本的某文件内容。
svn list -- 显示一个目录或某一版本存在的文件列表。
svn log -- 显示svn 的版本log，含作者、日期、路径等。
svn diff -- 显示特定修改的行级详细信息。

**list示例：**
```
svn list http://svn.test.com/svn     #查看目录中的文件。
svn list -v http://svn.test.com/svn  #查看详细的目录的信息(修订人,版本号,文件大小等)。
svn list [-v]                        #查看当前当前工作拷贝的版本库URL。
```
**cat示例：**
```
svn cat -r 4 test.c     #查看版本4中的文件test.c的内容,不进行比较。
```
**diff示例：**
```
svn diff               #什么都不加，会坚持本地代码和缓存在本地.svn目录下的信息的不同;信息太多，没啥用处。
svn diff -r 3          #比较你的本地代码和版本号为3的所有文件的不同。
svn diff -r 3 text.c   #比较你的本地代码和版本号为3的text.c文件的不同。
svn diff -r 5:6        #比较版本5和版本6之间所有文件的不同。
svn diff -r 5:6 text.c #比较版本5和版本6之间的text.c文件的变化。
svn diff -c 6 test.c    #比较版本5和版本6之间的text.c文件的变化。
```
**log示例**：
```
svn log         #什么都不加会显示所有版本commit的日志信息:版本、作者、日期、comment。
svn log -r 4:20 #只看版本4到版本20的日志信息，顺序显示。
svn log -r 20:5 #显示版本20到4之间的日志信息，逆序显示。
svn log test.c  #查看文件test.c的日志修改信息。
svn log -r 8 -v #显示版本8的详细修改日志，包括修改的所有文件列表信息。
svn log -r 8 -v -q   #显示版本8的详细提交日志，不包括comment。
svn log -v -r 88:866 #显示从版本88到版本866之间，当前代码目录下所有变更的详细信息 。
svn log -v dir  #查看目录的日志修改信息,需要加v。
svn log http://foo.com/svn/trunk/code/  #显示代码目录的日志信息。
```
# 常用命令
svn add file|dir -- 添加文件或整个目录
svn checkout -- 获取svn代码
svn commit  -- 提交本地修改代码
svn status    -- 查看本地修改代码情况：修改的或本地独有的文件详细信息
svn merge   -- 合并svn和本地代码
svn revert   -- 撤销本地修改代码
svn resolve -- 合并冲突代码
svn help [command] -- 查看svn帮助，或特定命令帮助

# svn diff个性化定制
meld是一个GUI的文本比较工具，比SVN自带的diff工具要好用很多。如何使用meld来替代SVN自带的diff呢？方法如下： 
1. 安装好meld
```
brew install Caskroom/cask/meld
```
2. 编辑~/.subversion目录下的config文件，将meld赋值给diff-cmd，如下所示： 
```
###   Set diff-cmd to the absolute path of your 'diff' program. 
###   This will override the compile-time default, which is to use 
###   Subversion's internal diffimplementation. 
diff-cmd = meld
```

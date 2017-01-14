---
title: Vusual Stdio Code 常用快捷键
categories : tools
tags : [visual stdio code, vsc]
date : 2016-06-30 10:38:30
---

Visual Studio Code是个牛逼的编辑器，启动非常快，完全可以用来代替其他文本文件编辑工具。又可以用来做开发，支持各种语言。

<!-- more -->

# 主命令框
最重要的功能就是F1或Ctrl+Shift+P打开的命令面板了，在这个命令框里可以执行VSCode的任何一条命令，可以查看每条命令对应的快捷键，甚至可以关闭这个编辑器。

# Ctrl+P 模式
在Ctrl+P下输入>又可以回到主命令框 Ctrl+Shift+P模式。

在Ctrl+P窗口下还可以
- 直接输入文件名，快速打开文件
- ? 列出当前可执行的动作
- ! 显示Errors或Warnings，也可以Ctrl+Shift+M
- : 跳转到行数，也可以Ctrl+G直接进入
- @ 跳转到symbol（搜索变量或者函数），也可以Ctrl+Shift+o直接进入
- @:根据分类跳转symbol，查找属性或函数，也可以Ctrl+Shift+o后输入:进入
- \# 根据名字查找symbol，也可以Ctrl+T

# 常用快捷键
- 打开一个新窗口： Ctrl+Shift+N
- 关闭窗口： Ctrl+Shift+W


- 新建文件 Ctrl+N
- 历史打开文件之间切换 Ctrl+Tab，Alt+Left，Alt+Right
- 切出一个新的编辑器（最多3个）Ctrl+\，也可以按住Ctrl鼠标点击Explorer里的文件名
- 左中右3个编辑器的快捷键Ctrl+1 Ctrl+2 Ctrl+3
- 3个编辑器之间循环切换 Ctrl+`
- 编辑器换位置，Ctrl+k然后按Left或Right


- ctrl+/ 注释
- 代码行缩进Ctrl+[， Ctrl+]
- Ctrl+C Ctrl+V如果不选中，默认复制或剪切一整行
- 代码格式化：Shift+Alt+F，或Ctrl+Shift+P后输入format code
- 修剪行尾空格Ctrl+K Ctrl+X
- 上下移动一行： Alt+Up 或 Alt+Down
- 向上向下复制一行： Shift+Alt+Up或Shift+Alt+Down
- 在当前行下边插入一行Ctrl+Enter
- 在当前行上方插入一行Ctrl+Shift+Enter
- shift+ctrl+[ 单个函数折叠
- shift+ctrl+] 单个函数展开
- shift+ctrl+alt+[ 整个文件函数折叠
- shift+ctrl+alt+] 整个文件函数展开
- shift+ctrl+K 删除一行


- 移动到文件结尾：Ctrl+End
- 移动到文件开头：Ctrl+Home
- 移动到后半个括号 Ctrl+Shift+]
- 选中当前行Ctrl+i
- 选择从光标到行尾Shift+End
- 选择从行首到光标处Shift+Home
- 删除光标右侧的所有字Ctrl+Delete
- Shrink/expand selection： Shift+Alt+Left和Shift+Alt+Right
- Multi-Cursor：可以连续选择多处，然后一起修改，Alt+Click添加cursor或者Ctrl+Alt+Down 或 Ctrl+Alt+Up
- 同时选中所有匹配的Ctrl+Shift+L
- Ctrl+D下一个匹配的也被选中(被我自定义成删除当前行了，见下边Ctrl+Shift+K)
- 回退上一个光标操作Ctrl+U


- F3	   向后查找
- Shift+F3   向前查找


- ctrl+click或F12 转到定义
- ctrl+hover      显示定义或声明
- shift+alt+F10   快速显示定义


- ctrl+F2      高亮所有选中的单词
- shift+ctrl+L 高亮所有选中的内容


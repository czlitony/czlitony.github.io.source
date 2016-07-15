---
title: Vmware vSphere虚拟机与主机共享剪贴板  
categories : other
tags : [Vmware]
---

默认情况下，Vmware vSphere已禁用针对ESXESXi的复制和粘贴操作，以防止公开已复制到剪贴板中的敏感数据。其实可以通过设置启用它，具体操作如下：

<!-- more -->


1. 使用 vSphere Client 登录到 vCenter Server 系统并选择虚拟机，虚拟机要在关闭状态下。

2. 选中虚拟机，单击右键编辑设置。

3. 选择选项 > 高级 > 常规，然后单击配置参数。

4. 单击添加行，并在“名称”和“值”列中键入以下值。名称值

	    isolation.tools.copy.disable     false
	    isolation.tools.paste.disable    false

5. 单击确定以关闭“配置参数”对话框，然后再次单击确定以关闭“虚拟机属性”对话框。

6. 重新启动虚拟机。

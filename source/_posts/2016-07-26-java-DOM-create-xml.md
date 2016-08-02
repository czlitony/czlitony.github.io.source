---
title: Java DOM生成xml文件
categories : java
tags : [XML， DOM]
date : 2016-07-26 9:38:30
---

我们不仅可以使用DOM的方式解析XML文件，同时也可以使用DOM的方式生成XML文件。本文主要内容是使用DOM的方式生成XML文件。

<!-- more -->

首先是创建DOM之中的各个节点，然后按照各自的层次添加这些节点。
DomCreateDoucment.java文件内容如下：
~~~
package com.app.dom;

import java.io.File;
import java.io.FileOutputStream;

import javax.xml.parsers.DocumentBuilder;
import javax.xml.parsers.DocumentBuilderFactory;
import javax.xml.transform.OutputKeys;
import javax.xml.transform.Transformer;
import javax.xml.transform.TransformerFactory;
import javax.xml.transform.dom.DOMSource;
import javax.xml.transform.stream.StreamResult;

import org.w3c.dom.Document;
import org.w3c.dom.Element;
import org.w3c.dom.Text;

public class DomCreateDoucment {

	public static void main(String[] args) throws Exception {

		DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
		DocumentBuilder builder = factory.newDocumentBuilder();
		Document doc = builder.newDocument();
		//构建XML中的节点
		Element root = doc.createElement("font");
		Element nameElement = doc.createElement("name");
		Text nameValue = doc.createTextNode("san");
		Element sizeElement = doc.createElement("size");
		sizeElement.setAttribute("unit", "px");
		Text sizeValue = doc.createTextNode("14");
       //按顺序添加各个节点
		doc.appendChild(root);
		root.appendChild(nameElement);
		nameElement.appendChild(nameValue);
		root.appendChild(sizeElement);
		sizeElement.appendChild(sizeValue);
		
		Transformer t=TransformerFactory.newInstance().newTransformer();
		//设置换行和缩进
		t.setOutputProperty(OutputKeys.INDENT,"yes");
		t.setOutputProperty(OutputKeys.METHOD, "xml");
		t.transform(new DOMSource(doc), new StreamResult(new FileOutputStream(new File("text.xml"))));

	}

}
~~~

编译：
在该文件所在目录下执行：
~~~
javac -d . DomCreateDoucment.java
~~~
执行成功的话会在当前路径下生成:com/app/dom/DomCreateDoucment.class

执行：
~~~
java com.app.dom.DomCreateDoucment
~~~

结果：
在当前路径下生成text.xml文件：
~~~
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<font>
<name>san</name>
<size unit="px">14</size>
</font>
~~~
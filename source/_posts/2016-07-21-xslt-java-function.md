---
title: xslt调用java类方法
categories : xslt
tags : [xml,xsl,xslt,xpath]
---

xslt中可以调用java的类方法，对这种引用了java方法的xslt的解析可以采用java JDK自带的 javax.xml.transform.本文对这种方法进行介绍。

<!-- more -->

主要的步骤为：
1. 编写待调用的java类**静态方法**
2. 在xsl文件的开头指定待引用的java类所在的包（如果不声明包的话，就填写类名）
3. 在xsl文件中通过"**前缀：类名.方法名**"的方式来调用
4. 用java jdk自带的javax.xml.transform类完成xslt转换

本文主要是为了演示怎样在xsl中调用java的方法，所以就不用xsl去转换某个xml文件了，仅仅是解析xsl文件而已。
本文用到的文件有MaxMinMedium.java、Transform.java、s.xsl等。
原始文件组织结构为：
~~~
test
  MaxMinMedium.java
  Transform.java
  s.xsl
  x.xml
~~~
 
 **声明:**文中代码为说明问题所编写，不保证一定可以运行～
 
 下面进入主题：

## 1. 编写java类
java相关的安装事宜可以参考这篇文章：
[https://www.digitalocean.com/community/tutorials/how-to-install-java-on-ubuntu-with-apt-get](https://www.digitalocean.com/community/tutorials/how-to-install-java-on-ubuntu-with-apt-get)
为了编译java文件，首先需要安装jdk, 在ubantu上面执行：
~~~
sudo apt-get install default-jdk
~~~
如果要运行java程序需要安装jre：
~~~
sudo apt-get install default-jre
~~~

想要理解本方法，需要对java包有所了解，简单来说java包一定程度上充当了命名空间的角色。本文中定义的java源文件为 MaxMinMedium.java：
~~~
package extensions;

import java.util.ArrayList;
import java.util.Iterator;
import java.util.Collections;
import java.util.List;

public class MaxMinMedium {
    private static ArrayList<Integer> valueList = new ArrayList<Integer>();

    //reset sum and valueList
    public static boolean reset() {
        valueList.clear();
        return true;
    }

    public static boolean calc() {
        if(valueList.size() > 0) {
            Collections.sort(valueList);
            return true;
        } else {
            return false;
        }
    }

    public static boolean insert(int val) {
        valueList.add(val);

        return true;
    }

    public static int getMax() {
        return valueList.get(valueList.size()-1);
    }
	
    public static int getMin() {
        return valueList.get(0);
    }

    public static int getMedium() {
        return valueList.get(valueList.size()/2);
    }
}
~~~
这个文件的开头package extensions是声明了一个extensions的包，在该文件所在的路径下执行：
~~~
javac -d . MaxMinMedium.java
~~~
将会在当前路径下产生 extensions 文件夹，在这个文件夹下会出现 MaxMinMedium.class 文件，这个就是java编译之后得到的文件。

可以在当前路径下新建其他的java文件，在这些源文件中均声明 package extensions，这样编译后会在extensions文件夹下出现相应的class文件。可以在这些不同的java文件中定义不同的类，各自完成相应的功能。
MaxMinMedium.java中的方法主要是获取一组数的最大值、最小值和中位数。

**注意：**类中待调用的方法需要是静态的！

## 2. 在xsl文件中指定java包
s.xsl 内容如下：
~~~
<?xml version="1.0" encoding="utf-8"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform"
    xmlns:javaExt="extensions"
    exclude-result-prefixes="javaExt">
    
    <xsl:if test="javaExt:MaxMinMedium.insert(1)" />
    <xsl:if test="javaExt:MaxMinMedium.insert(2)" />
    <xsl:if test="javaExt:MaxMinMedium.insert(3)" />
    <xsl:if test="javaExt:MaxMinMedium.insert(4)" />
    
    <xsl:if test="javaExt:MaxMinMedium.calc()" />
    
    <test>
        <max>
            <xsl:attribute name="value">
                <xsl:value-of select="javaExt:MaxMinMedium.getMax()" />
            </xsl:attribute>
        </max>
        <min>
            <xsl:attribute name="value">
                <xsl:value-of select="javaExt:MaxMinMedium.getMin()" />
            </xsl:attribute>
        </min>
        <medium>
            <xsl:attribute name="value">
                <xsl:value-of select="javaExt:MaxMinMedium.getMedium()" />
            </xsl:attribute>
        </medium>
    </test>
    
</xsl:stylesheet>
~~~
java包的引用采用这种方式：xmlns:javaExt="extensions"，这里的javaEx是在引用java方法是所要加的前缀，可以换成其他的名称；
exclude-result-prefixes="javaExt"这句话是可选的，不加这句话也是可以的，加上后更好，貌似为了避免和java本身的命名空间重叠。

## 3. 在xsl中调用java静态方法
调用方法在前面的第二步中可以看到，调用的方式为： **前缀:类名.方法名**，如
~~~
javaExt:MaxMinMedium.getMax()
~~~

**补充说明：**
这种调用方式是在java类中声明了包的时候所采用的，如果在java类中不声明包（将package extensions去掉）的时候，采用下面的方式：
xsl引用类：xmlns:javaExt="MaxMinMedium"
xsl调用方法：javaExt:getMax()
*这种不声明包的方式就要把所有的函数都写在一个java类中，不利于程序的解耦，所以不是很推荐，除非是比较简单单一的调用。*

## 4. 解析
解析的文件为 Transform.java，其中的内容如下：
~~~
//import needed transform packages
import javax.xml.transform.TransformerFactory;
import javax.xml.transform.Transformer;
import javax.xml.transform.stream.StreamSource;
import javax.xml.transform.stream.StreamResult;
import javax.xml.transform.TransformerException;
import javax.xml.transform.TransformerConfigurationException;

//import needed java packages
import java.io.FileOutputStream;
import java.io.FileNotFoundException;
import java.io.IOException;

public class Transform
{
    public static void main(String[] args)
    throws TransformerException, TransformerConfigurationException, 
           FileNotFoundException, IOException
    {  
        //create a transformer factory class.
        TransformerFactory tFactory = TransformerFactory.newInstance();

        //create an instance using the factory class
        Transformer transformer = tFactory.newTransformer(new StreamSource("s.xsl"));

        //call function transform from class transformer to complete conversion
        transformer.transform(new StreamSource("x.xml"), new StreamResult(new FileOutputStream("out.xml")));

        //print the message telling user that the conversion is done. 
        System.out.println("************* The result is in out.xml*************");
    }
}
~~~
(这段代码中的x.ml其实没用到，这个转换方法能否成功不能百分百保证...)

下面编译该文件：
~~~
javac Transform.java
~~~
然后会在当前路径生成 Transform.class文件。

下面调用该文件进行xsl转换：
~~~
java Transform
~~~

如果没有错误的话理应会看到控制台输出一段文字：The result is in out.xml，然后在当前路径下会生成out.xml文件。
out.xml文件中的内容为：
~~~
<?xml version="1.0" encoding="utf-8"?>
<test>
<max value="4"/>
<min value="1"/>
<medium count="2">
</test>
~~~

至此，整个的文件夹的结构为：
~~~
test
    MaxMinMedium.java
    Transform.java
    Transform.class
    s.xsl
    x.xml
    out.xml
    extensions
        MaxMinMedium.class
~~~

以上就是对xsl调用java方法的介绍，有待进一步研究～

参考链接：
[http://unmi.cc/xslt-call-java-method/](http://unmi.cc/xslt-call-java-method/)





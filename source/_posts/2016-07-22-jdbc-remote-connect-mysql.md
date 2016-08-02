---
title: JDBC远程连接MySql
categories : java
tags : [JDBC， MySql]
date : 2016-07-22 9:38:30
---

本文主要内容是开启MySql数据库的远程连接功能，然后远程通过JDBC来连接上数据库，进行简单的查询操作。

<!-- more -->

## 开启mysql远程连接功能
mysql默认的远程连接功能貌似是关闭的，如果需要远程连接的话需要开通。开通的过程分为以下几步：
### 进入
在控制台输入：
~~~
mysql -u root -p
~~~
执行命令后会提示输入密码，一般不需要输入，直接回车即可。

### 查看当前用户
接着第1步，继续输入以下指令，注意；不要省略：
~~~
use mysql;
select host,user,password from user;
~~~
然后控制台会输出当前db的一些用户信息，如：
~~~
MariaDB [mysql]> select host,user,password from user;
+-----------+-------------+-------------------------------------------+
| host      | user        | password                                  |
+-----------+-------------+-------------------------------------------+
| localhost | root        |                                           |
| (none)    | root        |                                           |
| 127.0.0.1 | root        |                                           |
| ::1       | root        |                                           |
| localhost |             |                                           |
| (none)    |             |                                           |
| %         | replication | *51125B3597BEE0FC43E0BCBFEE002EF8641B44CF |
| localhost | DbAdmin     | *0DF17D910FD01DCE47EC3F384C33306F50E8CE54 |
| %         | root        | *81F5E21E35407D884A6CD4A731AEBFB6AF209E1B |
+-----------+-------------+-------------------------------------------+
~~~
password栏为空的一般都是不用密码的，有内容的是加密后的密码。下面第三步增加可以远程访问该db的用户。

### 增加用户
**grant命令：**
 grant 权限1,权限2,…权限n on 数据库名称.表名称 to 用户名@用户地址 identified by '连接口令';

如果使任何主机都能以root用户，password为密码连接的话就输入以下命令(命令不区分大小写)：
~~~
GRANT ALL PRIVILEGES ON *.* TO root@"%" IDENTIFIED BY "password"; 
~~~
为了使设置生效，输入以下命令：
~~~
flush privileges; 
~~~

至此就可以远程连接了，注意用户名=root，密码=password.

## JDBC远程连接MySql数据库
### 代码
啥都不说了，看代码吧，PersonalBookmarks.java：
~~~
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Connection;
import java.sql.Statement;
 
public class PersonalBookmarks {
    public static void main(String[] args) throws Exception {
        Connection conn = null;
        String sql;

        // MySql jdbc url format: jdbc:mysql://host:port/database_name?param=value
        // Set useUnicode and characterEncoding to avoid messy code
        String url = "jdbc:mysql://10.103.62.159:3306/bookmarks?"
            + "user=root&password=password&useUnicode=true&characterEncoding=UTF8"; //这个url里面设置所要访问的数据库所在的主机以及所要访问的数据库，还有用户名和密码，这个用户名密码就是上面设置的

        try {
            // Load MySql driver.Each of those three ways is fine.
            Class.forName("com.mysql.jdbc.Driver");
            // or:
            // com.mysql.jdbc.Driver driver = new com.mysql.jdbc.Driver();
            // or：
            // new com.mysql.jdbc.Driver();

            //for test
            System.out.println("Load MySQL Driver successfully!");

            // One Connection repersents a database connection.
            conn = DriverManager.getConnection(url);

            // There are many functions in Statement, such as executeQuery , executeUpdate etc.
            Statement stmt = conn.createStatement();
            System.out.println("Connect to database successfully!");// for test

            // sql = "select a.url,b.clienttype from bookmarks as a,customlinks as b where a.bkmrkid=b.pslinkid";
            // ResultSet data = stmt.executeQuery(sql);//Query for data
            
            // while (data.next()) {    
            //     System.out.println(data.getString("url")); // for test
            //     System.out.println(data.getString("clienttype")); // for test

            //     String[] temp = data.getString("url").split(":");
            //     String shortcutType = temp[0];
            //     String clientType = data.getString("clienttype");

            //     if (shortcutTypeMap.containsKey(shortcutType) && clientTypeMap.containsKey(clientType)) {
            //         result[shortcutTypeMap.get(shortcutType)][clientTypeMap.get(clientType)]++;
            //     }
            // }
        } catch (SQLException e) {
            System.out.println ("MySQL operation mistake");
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            conn.close();
        }

    }
    
}
~~~

### 编译：
~~~
javac PersonalBookmarks.java
~~~

### 运行：
要想运行这段代码，需要*mysql-connector-java-5.1.39-bin.jar*这个jar包，可以去mysql官网下载，下载之后将这个jar包放在jdbc.java同一路径，
之后执行：
~~~
java -cp .:mysql-connector-java-5.1.39-bin.jar PersonalBookmarks
~~~

执行后控制台看到以下输出就代表成功了。
~~~
Load MySQL Driver successfully!
Connect to database successfully!
~~~
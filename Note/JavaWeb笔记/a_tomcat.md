---
date: 2015-11-29 17:09
status: public
title: a_tomcat
---

# 1. 部署并启动 tomcat 服务器.
1). 解压 apache-tomcat-6.0.16.zip 到一个非中文目录下
2). 配置一个环境变量. java_home(指向 JDK 安装的根目录) 或 jre_home
3). 通过双击 apache-tomcat-6.0.16\bin 目录下的 startup.bat, 启动服务器
4). 可以在浏览器中输入 localhost:8080 来检验 Tomcat 安装是否正确. 
5). 若已经启动了一个 Tomcat 应用, 若再启动同一个 Tomcat 应用, 会抛出异常:  
java.net.BindException: Address already in use: JVM_Bind:8080
因为端口已经被占用了.

# 2. Tomcat: 开源的 Servlet 容器. 

# 3. 可以通过修改 server.xml 文件中的配置信息来修改 Tomcat 服务器的端口号:
```xml
<Connector port="8989" protocol="HTTP/1.1" 
           connectionTimeout="20000" 
           redirectPort="8443" />
```

把 port 的值改为其他的端口号即可.

# 4. 为了可以在任意目录下启动 Tomcat 服务器
4.1 把 D:\apache-tomcat-6.0.16\bin 添加到 path 下

添加成功后, 若在任意目录下通过 startup 启动 Tomcat 服务器, 会有提示:

The CATALINA_HOME environment variable is not defined correctly
This environment variable is needed to run this program

即: 要求设置 CATALINA_HOME 这个环境变量

# 5. 设置 CATALINA_HOME 环境变量:
5.1 通过阅读 startup.bat 批处理文件, 可知 CATALINA_HOME 环境变量下有一个 bin 目录,
而 bin 目录又有一个 catalina.bat 文件, 由此可知 CATALINA_HOME 指向的应该是 Tomcat 
安装的根目录

于是把 Tomcat 的根目录设为 CATALINA_HOME 环境变量. 

此时即可以在 任意 目录下通过 startup.bat、shutdown.bat 启动、关闭 Tomcat 服务器. 

# 6. 继续阅读 startup.bat 文件， 发现启动 tomcat 服务器的是 catalina.bat 文件

在命令行窗口直接输入 catalina, 提示如下:
```
  commands:
  debug             Start Catalina in a debugger
  debug -security   Debug Catalina with a security manager
  jpda start        Start Catalina under JPDA debugger
  run               Start Catalina in the current window
  run -security     Start in the current window with security manager
  start             Start Catalina in a separate window
  start -security   Start in a separate window with security manager
  stop              Stop Catalina
  version           What version of tomcat are you running?
```

catalina 命令有如上的可选参数.

run: 在 catalina 同一个命令行窗口下启动服务器. 
start: 开启一个新窗口启动服务器
stop: 关闭服务器.

# 7. 第一个 WEB 应用程序: 开发, 并部署到 Tomcat 服务器下运行

1). 在 Eclipse 新建一个 Java Project
2). 在 Java 项目下创建 WEB 开发的目录结构

```
 -WebContent
 	-WEB-INF
		-classes (编译后的 class 文件必须放在该目录下.)
		-lib
		-web.xml (从 apache-tomcat-6.0.16\webapps\docs\WEB-INF 中复制过来,
		可以不做修改)
	html 页面
	JSP 页面
	图片...
```	
3). 在 src 下新建一个 Person 类
```java
package com.atguigu.test;

public class Person {

	public String getPersonInfo(){
		return "person info...";
	}

}
```

4). 手工的 Person 类对应的 class 文件(含包)复制到 classes 目录下

可以通过修改默认的输出目录达到自动把编译好的 class 放到 classes 目录下:

5). 在 WebContent 目录下新建一个 JSP 文件:
```jsp
<%@page import="com.atguigu.test.Person"%>
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1"%>


<% 
	Person person = new Person();
	System.out.print(person.getPersonInfo()); 
%>
```
6). 把 WebContent 目录复制到 Tomcat 的 webapps 目录下, 并改名为: helloworld

7). 在浏览器的地址栏中输入: http://localhost:8989/helloworld/hello.jsp 即可
看到命令行的打印信息

8). 配置任意目录下的Web应用程序:
在 conf 目录下依次创建 catalina\localhost 目录，然后在 localhost 目录下为 test 
这个Web应用程序建立 test.xml 文件，编辑这个文件输入以下内容
```
<?xml version="1.0" encoding="UTF-8"?>
<Context 
docBase="E:\\Java\\Source\\atguigu\\java-1\\firstWebApp\\WebContent" 
reloadable="true"/> 
```
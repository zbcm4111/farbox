---
date: 2015-11-29 17:48
status: public
title: d_jsp
---

# 1. JSP 指令: 
JSP指令（directive）是为JSP引擎而设计的, 它们并不直接产生任何可见输出, 而只是告诉引擎如何处理JSP页面中的其余部分。

# 2. 在目前的JSP 2.0中，定义了page、include 和 taglib这三种指令

# 3. page 指令:

1). page指令用于定义JSP页面的各种属性, 无论page指令出现在JSP页面中的什么地方, 
它作用的都是整个JSP页面, 为了保持程序的可读性和遵循良好的编程习惯, page指令最好是放在整个JSP页面的起始位置。 

2). page 指令常用的属性: 

①. import 属性: 指定当前 JSP 页面对应的 Servlet 需要导入的类. 
`<%@page import="java.text.DateFormat"%>`

②. session 属性: 取值为 true 或 false, 指定当前页面的 session 隐藏变量是否可用, 也可以说访问当前页面时是否一定要生成 HttpSession
对象. 
`<%@ page session="false" %> `

③. errorPage 和 isErrorPage: 
	> errorPage 指定若当前页面出现错误的实际响应页面时什么. 其中 / 表示的是当前 WEB 应用的根目录. 
	`<%@ page errorPage="/error.jsp" %> `

	> 在响应 error.jsp 时, JSP 引擎使用的请求转发的方式. 
	
    > isErrorPage 指定当前页面是否为错误处理页面, 可以说明当前页面是否可以使用 exception 隐藏变量. 需要注意的是: 若指定 
	isErrorPage="true", 并使用 exception 的方法了, 一般不建议能够直接访问该页面. 
	
	> 如何使客户不能直接访问某一个页面呢 ? 对于 Tomcat 服务器而言, WEB-INF 下的文件是不能通过在浏览器中直接输入地址的方式
	来访问的. 但通过请求的转发是可以的!
	
	> 还可以在 web.xml 文件中配置错误页面: 
```xml
	<error-page>
  	<!-- 指定出错的代码: 404 没有指定的资源, 500 内部错误. -->
	  	<error-code>404</error-code>
	  	<!-- 指定响应页面的位置 -->
	  	<location>/WEB-INF/error.jsp</location>
	</error-page>
	  
	<error-page>
	  	<!-- 指定异常的类型 -->
	  	<exception-type>java.lang.ArithmeticException</exception-type>
	  	<location>/WEB-INF/error.jsp</location>
	</error-page>
```
④. contentType: 指定当前 JSP 页面的响应类型. 实际调用的是 response.setContentType("text/html; charset=UTF-8");
通常情况下, 对于 JSP 页面而言其取值均为 text/html; charset=UTF-8. charset 指定返回的页面的字符编码是什么. 通常取值为 UTF-8

⑤. pageEncoding: 指定当前 JSP 页面的字符编码. 通常情况下该值和 contentType 中的 charset 一致. 

⑥. isELIgnored: 指定当前 JSP 页面是否可以使用 EL 表达式. 通常取值为 false. 
	
# 3. include 指令: <%@ include file="b.jsp" %>

1). include 指令用于通知 JSP 引擎在翻译当前 JSP 页面时将其他文件中的内容合并进当前 JSP 页面转换成的 Servlet 源文件中,
这种在源文件级别进行引入的方式称之为静态引入, 当前JSP页面与静态引入的页面紧密结合为一个Servlet。

2). file属性的设置值必须使用相对路径

3). 如果以 / 开头，表示相对于当前WEB应用程序的根目录（注意不是站点根目录），否则，表示相对于当前文件。

# 4. jsp:incluce 标签:

1).` <jsp:include page="b.jsp"></jsp:include>`
	
2). 动态引入: 并不是像 include 指令生成一个 Servlet 源文件, 而是生成两个 Servlet 源文件, 然后通过一个方法的方式把目标页面包含
进来. 

org.apache.jasper.runtime.JspRuntimeLibrary.include(request, response, "b.jsp", out, false);

# 5. jsp:forward:

1).

`<jsp:forward page="/include/b.jsp"></jsp:forward>	   `

相当于. 
```jsp
<% 
	request.getRequestDispatcher("/include/b.jsp").forward(request, response);
%>
```
2). 但使用 jsp:forward 可以使用 jsp:param 子标签向 b.jsp 传入一些参数. 同样 jsp:include 也可以使用 jsp:param 子标签. 
```jsp
<jsp:forward page="/include/b.jsp">
	<jsp:param value="abcd" name="username"/>
</jsp:forward>	
```
OR
```jsp
<jsp:include page="/include/b.jsp">
	<jsp:param value="abcd" name="username"/>
</jsp:include>
```
在  b.jsp 页面可以通过 request.getParameter("username") 获取到传入的请求参数. 

# 6. 关于中文乱码:

1). 在 JSP 页面上输入中文, 请求页面后不出现乱码: 保证  contentType="text/html; charset=UTF-8", 
    pageEncoding="UTF-8" charset 和 pageEncoding 的编码一致, 且都支持中文. 通常建议取值为UTF-8
    
还需保证浏览器的显示的字符编码也和请求的 JSP 页面的编码一致. 
        
2). 获取中文参数值: 默认参数在传输过程中使用的编码为 ISO-8859-1

①. 对于 POST 请求: 只要在获取请求信息之前(在调用 request.getParameter 或者是 request.getReader 等), 
调用 request.setCharacterEncoding("UTF-8") 即可.

②. 对于 GET 请求: 前面的方式对于 GET 无效. 可以通过修改 Tomcat 的 server.xml 文件的方式. 

参照 http://localhost:8989/docs/config/index.html 文档的 useBodyEncodingForURI 属性. 
为 Connector 节点添加 useBodyEncodingForURI="true" 属性即可. 
```xml
<Connector connectionTimeout="20000" port="8989" protocol="HTTP/1.1" redirectPort="8443" useBodyEncodingForURI="true"/>
```
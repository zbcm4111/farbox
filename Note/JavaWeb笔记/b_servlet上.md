---
date: 2015-11-29 17:11
status: public
title: b_servlet上
---

# 1. 使用 JavaEE 版的 Eclipse 开发动态的 WEB 工程(JavaWEB 项目)
1). 把开发选项切换到 JavaEE
2). 可以在 Window -> Show View 中找到 Package Explorer, 并把其拖拽到开发区的左边
3). 在 Servers 面板中新建 Tomcat 服务器. 一定要关联到 Tomcat 安装的根目录
4). 新建一个 Dynamic Web Project. 其中 Target Runtime 需选择 Tomcat6.0 
5). 开发 Java WEB 应用
6). 可以通过 run on server  来运行 WEB 项目. 

# 2. Servlet 的 HelloWorld
1). 创建一个 Servlet 接口的实现类. 
public class HelloServlet implements Servlet

2). 在 web.xml 文件中配置和映射这个 Servlet
```xml
	<!-- 配置和映射 Servlet -->
	<servlet>
		<!-- Servlet 注册的名字 -->
		<servlet-name>helloServlet</servlet-name>
		<!-- Servlet 的全类名 -->
		<servlet-class>com.atguigu.javaweb.HelloServlet</servlet-class>
	</servlet>
	
	<servlet-mapping>
		<!-- 需要和某一个 servlet 节点的 serlvet-name 子节点的文本节点一致 -->
		<servlet-name>helloServlet</servlet-name>
		<!-- 映射具体的访问路径: / 代表当前 WEB 应用的根目录. -->
		<url-pattern>/hello</url-pattern>
	</servlet-mapping>
```
# 3. Servlet 容器: 运行 Servlet、JSP、Filter 等的软件环境. 
1). 可以来创建 Servlet, 并调用 Servlet 的相关生命周期方法. 
2). JSP, Filter, Listener, Tag ...

# 4. Servlet 生命周期的方法: 以下方法都是由 Serlvet 容器负责调用. 
1). 构造器: 只被调用一次. 只有第一次请求 Servlet 时, 创建 Servlet 的实例. 调用构造器. 
这说明 Serlvet 的单实例的!
2). init 方法: 只被调用一次. 在创建好实例后立即被调用. 用于初始化当前 Servlet. 
3). service: 被多次调用. 每次请求都会调用 service 方法. 实际用于响应请求的. 
4). destroy: 只被调用一次. 在当前 Servlet 所在的 WEB 应用被卸载前调用. 用于释放当前 Servlet 所占用的资源. 

# 5. load-on-startup 参数:
1).  配置在 servlet 节点中:
```xml
	<servlet>
		<!-- Servlet 注册的名字 -->
		<servlet-name>secondServlet</servlet-name>
		<!-- Servlet 的全类名 -->
		<servlet-class>com.atguigu.javaweb.SecondServlet</servlet-class>
		<!-- 可以指定 Servlet 被创建的时机 -->
		<load-on-startup>2</load-on-startup>
	</servlet>
```	
2). load-on-startup: 可以指定 Serlvet 被创建的时机. 若为负数, 则在第一次请求时被创建.若为 0 或正数, 则在当前 WEB 应用被
Serlvet 容器加载时创建实例, 且数组越小越早被创建. 

# 6. 关于 serlvet-mapping：

1). 同一个Servlet可以被映射到多个URL上，即多个 <servlet-mapping> 元素的<servlet-name>子元素的设置值可以是同一个
Servlet的注册名。 

2). 在Servlet映射到的URL中也可以使用 * 通配符，但是只能有两种固定的格式：
一种格式是“*.扩展名”，另一种格式是以正斜杠（/）开头并以“/*”结尾。
```xml
<servlet-mapping>
	<servlet-name>secondServlet</servlet-name>
	<url-pattern>/*</url-pattern>
</servlet-mapping>
```
OR
```xml
<servlet-mapping>
	<servlet-name>secondServlet</servlet-name>
	<url-pattern>*.do</url-pattern>
</servlet-mapping>
```
注意: 以下的既带 / 又带扩展名的不合法. 
```xml
<servlet-mapping>
	<servlet-name>secondServlet</servlet-name>
	<url-pattern>/*.action</url-pattern>
</servlet-mapping>
```
# 7. ServletConfig: 封装了 Serlvet 的配置信息, 并且可以获取 ServletContext 对象

1). 配置 Serlvet 的初始化参数
```xml
	<servlet>
		<servlet-name>helloServlet</servlet-name>
		<servlet-class>com.atguigu.javaweb.HelloServlet</servlet-class>
		
		<!-- 配置 Serlvet 的初始化参数。 且节点必须在 load-on-startup 节点的前面 -->
		<init-param>
			<!-- 参数名 -->
			<param-name>user</param-name>
			<!-- 参数值 -->
			<param-value>root</param-value>
		</init-param>
		
		<init-param>
			<param-name>password</param-name>
			<param-value>1230</param-value>
		</init-param>
		
		<load-on-startup>-1</load-on-startup>
		
	</servlet>
```
2). 获取初始化参数: 

	> getInitParameter(String name): 获取指定参数名的初始化参数
    > getInitParameterNames(): 获取参数名组成的 Enumeration 对象. 
```java
    String user = servletConfig.getInitParameter("user");
	System.out.println("user： " + user);
	
	Enumeration<String> names = servletConfig.getInitParameterNames();
	while(names.hasMoreElements()){
		String name = names.nextElement();
		String value = servletConfig.getInitParameter(name);
		System.out.println("^^" + name + ": " + value);
	}
```
3). 获取 Serlvet 的配置名称(了解)

# 8. ServletContext

1). 可以由  SerlvetConfig 获取:

`ServletContext servletContext = servletConfig.getServletContext();`


2). 该对象代表当前 WEB 应用: 可以认为 SerlvetContext 是当前 WEB 应用的一个大管家. 可以从中获取到当前 WEB 应用的各个方面的信息.

①. 获取当前 WEB 应用的初始化参数

设置初始化参数: 可以为所有的 Servlet 所获取, 而 Servlet 的初始化参数只用那个 Serlvet 可以获取. 
```xml
<!-- 配置当前 WEB 应用的初始化参数 -->
<context-param>
	<param-name>driver</param-name>
	<param-value>com.mysql.jdbc.Driver</param-value>
</context-param>
```
方法:

getInitParameter
getInitParameterNames	

代码:
```java
ServletContext servletContext = servletConfig.getServletContext();
		
String driver = servletContext.getInitParameter("driver");
System.out.println("driver:" + driver);

Enumeration<String> names2 = servletContext.getInitParameterNames();
while(names2.hasMoreElements()){
	String name = names2.nextElement();
	System.out.println("-->" + name); 
}
```
②. 获取当前 WEB 应用的某一个文件在服务器上的绝对路径, 而不是部署前的路径

getRealPath(String path);

代码:
```java
String realPath = servletContext.getRealPath("/note.txt");
System.out.println(realPath);
```
③. 获取当前 WEB 应用的名称: 

getContextPath()

代码:
```java
String contextPath = servletContext.getContextPath();
System.out.println(contextPath); 
```
④. 获取当前 WEB 应用的某一个文件对应的输入流. 

getResourceAsStream(String path): path 的 / 为当前 WEB 应用的根目录. 

代码:

InputStream is2 = servletContext.getResourceAsStream("/WEB-INF/classes/jdbc.properties");
	
⑤. 和 attribute 相关的几个方法: 	

# 9. GET 请求和 POST 请求:

1). 使用GET方式传递参数:

①. 在浏览器地址栏中输入某个URL地址或单击网页上的一个超链接时，浏览器发出的HTTP请求消息的请求方式为GET。 
②. 如果网页中的<form>表单元素的 method 属性被设置为了“GET”，浏览器提交这个FORM表单时生成的HTTP请求消息的请求方式也为GET。 
③. 使用GET请求方式给WEB服务器传递参数的格式：  

http://www.atguigu.com/counter.jsp?name=lc&password=123

④. 使用GET方式传送的数据量一般限制在 1KB 以下。 

2). 使用 POST 方式传递参数:

①. POST 请求方式主要用于向 WEB 服务器端程序提交 FORM 表单中的数据: form 表单的 method 置为 POST
②. POST 方式将各个表单字段元素及其数据作为 HTTP 消息的实体内容发送给 WEB 服务器，传送的数据量要比使用GET方式传送的数据量大得多。  
```
POST /counter.jsp HTTP/1.1
referer: http://localhost:8080/Register.html
content-type: application/x-www-form-urlencoded
host: localhost:8080
content-length: 43

name=zhangsan&password=123              --请求体中传递参数. 
```
# 10. 如何在 Serlvet 中获取请求信息:

1). Servlet 的 service() 方法用于应答请求: 因为每次请求都会调用 service() 方法
```
public void service(ServletRequest request, ServletResponse response)
			throws ServletException, IOException
```	
ServletRequest: 封装了请求信息. 可以从中获取到任何的请求信息.
ServletResponse: 封装了响应信息, 如果想给用户什么响应, 具体可以使用该接口的方法实现. 

这两个接口的实现类都是服务器给予实现的, 并在服务器调用 service 方法时传入. 

2). ServletRequest: 封装了请求信息. 可以从中获取到任何的请求信息.

①. 获取请求参数: 

	> String getParameter(String name): 根据请求参数的名字, 返回参数值. 
	若请求参数有多个值(例如 checkbox), 该方法只能获取到第一个提交的值. 

	> String[] getParameterValues(String name): 根据请求参数的名字, 返回请求参数对应的字符串数组. 
	
	> Enumeration getParameterNames(): 返回参数名对应的 Enumeration 对象, 
	类似于 ServletConfig(或 ServletContext) 的 getInitParameterNames() 方法. 

	> Map getParameterMap(): 返回请求参数的键值对: key: 参数名,  value: 参数值, String 数组类型. 

②. 获取请求的 URI:

	HttpServletRequest httpServletRequest = (HttpServletRequest) request;
		
	String requestURI = httpServletRequest.getRequestURI();
	System.out.println(requestURI); //  /day_29/loginServlet

③. 获取请求方式: 

    String method = httpServletRequest.getMethod();
	System.out.println(method); //GET
	
④. 若是一个 GET 请求, 获取请求参数对应的那个字符串, 即 ? 后的那个字符串. 

	String queryString = httpServletRequest.getQueryString();
	System.out.println(queryString); //user=atguigu&password=123456&interesting=game&interesting=party&interesting=shopping

⑤. 获取请求的 Serlvet 的映射路径 
  
    String servletPath = httpServletRequest.getServletPath();
    System.out.println(servletPath);  //  /loginServlet
   
⑥. 和 attribute 相关的几个方法: 	   

3). HttpServletRequest: 是 SerlvetRequest 的子接口. 针对于 HTTP 请求所定义. 里边包含了大量获取 HTTP 请求相关的方法. 
 
4). ServletResponse: 封装了响应信息, 如果想给用户什么响应, 具体可以使用该接口的方法实现. 

①. *getWriter(): 返回 PrintWriter 对象. 调用该对象的 print() 方法, 将把 print() 中的参数直接打印
到客户的浏览器上. 

②. 设置响应的内容类型: response.setContentType("application/msword");

③. void sendRedirect(String location): 请求的重定向. (此方法为 HttpServletResponse 中定义.)

-----------------------------------------------------------------------------

在 web.xml 文件中设置两个 WEB 应用的初始化参数, user, password.
定义一个 login.html, 里边定义两个请求字段: user, password. 发送请求到 loginServlet
在创建一个 LoginServlet, 在其中获取请求的 user, password. 比对其和 web.xml 文件中定义的请求参数是否一致
若一致, 响应 Hello:xxx, 若不一致, 响应 Sorry: xxx  xxx 为 user.

-----------------------------------------------------------------------------
---
date: 2015-11-29 17:41
status: public
title: c_servlet下
---

# 1. GenericServlet:

1). 是一个 Serlvet. 是 Servlet 接口和 ServletConfig 接口的实现类. 但是一个抽象类. 其中的 service 方法为抽象方法

2). 如果新建的 Servlet 程序直接继承 GenericSerlvet 会使开发更简洁.

3). 具体实现:

①. 在 GenericServlet 中声明了一个 SerlvetConfig 类型的成员变量, 在 init(ServletConfig) 方法中对其进行了初始化 
②. 利用 servletConfig 成员变量的方法实现了 ServletConfig 接口的方法
③. 还定义了一个 init() 方法, 在 init(SerlvetConfig) 方法中对其进行调用, 子类可以直接覆盖 init() 在其中实现对 Servlet 的初始化. 
④. 不建议直接覆盖 init(ServletConfig), 因为如果忘记编写 super.init(config); 而还是用了 SerlvetConfig 接口的方法,
则会出现空指针异常. 
⑤. 新建的 init(){} 并非 Serlvet 的生命周期方法. 而 init(ServletConfig) 是生命周期相关的方法. 
```java
public abstract class GenericServlet implements Servlet, ServletConfig {

	/** 以下方法为 Servlet 接口的方法 **/
	@Override
	public void destroy() {}

	@Override
	public ServletConfig getServletConfig() {
		return servletConfig;
	}

	@Override
	public String getServletInfo() {
		return null;
	}

	private ServletConfig servletConfig;
	
	@Override
	public void init(ServletConfig arg0) throws ServletException {
		this.servletConfig = arg0;
		init();
	}

	public void init() throws ServletException{}

	@Override
	public abstract void service(ServletRequest arg0, ServletResponse arg1)
			throws ServletException, IOException;

	/** 以下方法为 ServletConfig 接口的方法 **/
	@Override
	public String getInitParameter(String arg0) {
		return servletConfig.getInitParameter(arg0);
	}

	@Override
	public Enumeration getInitParameterNames() {
		return servletConfig.getInitParameterNames();
	}

	@Override
	public ServletContext getServletContext() {
		return servletConfig.getServletContext();
	}

	@Override
	public String getServletName() {
		return servletConfig.getServletName();
	}

}
```
# 2. HttpServlet:

1). 是一个 Servlet, 继承自 GenericServlet. 针对于 HTTP 协议所定制. 

2). 在 service() 方法中直接把 ServletReuqest 和  ServletResponse 转为 HttpServletRequest 和 HttpServletResponse.
并调用了重载的 service(HttpServletRequest, HttpServletResponse)

在 service(HttpServletRequest, HttpServletResponse) 获取了请求方式: request.getMethod(). 根据请求方式有创建了
doXxx() 方法(xxx 为具体的请求方式, 比如 doGet, doPost)
```java
@Override
 public void service(ServletRequest req, ServletResponse res)
    throws ServletException, IOException {

    HttpServletRequest  request;
    HttpServletResponse response;
    
    try {
        request = (HttpServletRequest) req;
        response = (HttpServletResponse) res;
    } catch (ClassCastException e) {
        throw new ServletException("non-HTTP request or response");
    }
    service(request, response);
}

public void service(HttpServletRequest request, HttpServletResponse response)
		throws ServletException, IOException {
	//1. 获取请求方式.
	String method = request.getMethod();
	
	//2. 根据请求方式再调用对应的处理方法
	if("GET".equalsIgnoreCase(method)){
		doGet(request, response);
	}else if("POST".equalsIgnoreCase(method)){
		doPost(request, response);
	}
}

public void doPost(HttpServletRequest request, HttpServletResponse response) 
		throws ServletException, IOException{
	// TODO Auto-generated method stub
	
}

public void doGet(HttpServletRequest request, HttpServletResponse response) 
		throws ServletException, IOException {
	// TODO Auto-generated method stub
	
}
```
3). 实际开发中, 直接继承 HttpServlet, 并根据请求方式复写 doXxx() 方法即可. 

4). 好处: 直接由针对性的覆盖 doXxx() 方法; 直接使用 HttpServletRequest 和  HttpServletResponse, 不再需要强转. 
# 3. 

-----------------------------------------------------------------------------

在 MySQL 数据库中创建一个 test_users 数据表, 添加 3 个字段: id, user, password. 并录入几条记录. 

定义一个 login.html, 里边定义两个请求字段: user, password. 发送请求到 loginServlet
在创建一个 LoginServlet(需要继承自 HttpServlet, 并重写其 doPost 方法), 
在其中获取请求的 user, password. 

利用 JDBC 从 test_users 中查询有没有和页面输入的 user, password 对应的记录

SELECT count(id) FROM test_users WHERE user = ? AND password = ?

若有, 响应 Hello:xxx, 若没有, 响应 Sorry: xxx  xxx 为 user.

-----------------------------------------------------------------------------
# 4. 请求的转发和重定向:

1). 本质区别: 请求的转发只发出了一次请求, 而重定向则发出了两次请求. 

具体:

①. 请求的转发: 地址栏是初次发出请求的地址.
       请求的重定向: 地址栏不再是初次发出的请求地址. 地址栏为最后响应的那个地址 
       
②. 请求转发: 在最终的 Servlet 中, request 对象和中转的那个 request 是同一个对象. 
       请求的重定向: 在最终的 Servlet 中, request 对象和中转的那个 request 不是同一个对象.       
   
③. 请求的转发: 只能转发给当前 WEB 应用的的资源
       请求的重定向: 可以重定向到任何资源. 
       
④. 请求的转发: / 代表的是当前 WEB 应用的根目录
       请求的重定向: / 代表的是当前 WEB 站点的根目录. 
# 5. JSP:

1). WHY: 

JSP 是简 Servlet 编写的一种技术, 它将 Java 代码和 HTML 语句混合在同一个文件中编写,
只对网页中的要动态产生的内容采用 Java 代码来编写，而对固定不变的静态内容采用普通静态 HTML 页面的方式编写。 

2). Java Server Page: Java 服务器端网页. 在 HTML 页面中编写 Java 代码的页面. 

2). helloworld: 

新建一个 JSP 页面, 在 body 节点内的 <% %> 即可编写 Java 代码.
```jsp
<body>

	<% 
		Date date = new Date();
		System.out.print(date); 
	%>

</body>
```
3). JSP 可以放置在 WEB 应用程序中的除了 WEB-INF 及其子目录外的其他任何目录中,
JSP 页面的访问路径与普通 HTML 页面的访问路径形式也完全一样。

4). JSP的运行原理: JSP 本质上是一个 Servlet.

每个JSP 页面在第一次被访问时, JSP 引擎将它翻译成一个 Servlet 源程序, 接着再把这个 Servlet 源程序编译成 Servlet 的 class 类文件.
然后再由WEB容器（Servlet引擎）像调用普通Servlet程序一样的方式来装载和解释执行这个由JSP页面翻译成的Servlet程序。 

5). JSP 页面的隐含变量: 没有声明就可以使用的对象. JSP页面一共有 9 个隐含对象. 
```java
public void _jspService(HttpServletRequest request, HttpServletResponse response)
        throws java.io.IOException, ServletException {

    PageContext pageContext = null;
    HttpSession session = null;
    ServletContext application = null;
    ServletConfig config = null;
    JspWriter out = null;
    Object page = this;

	//...
	
	//使用  <% %> 编写的代码在此位置. 可以用到 request, response, pageContext, session
	//application, config, out, page 这 8 个隐含对象. (实际上还可以使用一个叫 exception 的隐含对象)
    
}
```
①. request: HttpServletRequest 的一个对象. *
②. response: HttpServletResponse 的一个对象(在 JSP 页面中几乎不会调用 response 的任何方法.)

③. pageContext: 页面的上下文, 是 PageContext 的一个对象. 可以从该对象中获取到其他 8 个隐含对象. 也可以从中获取到当前
页面的其他信息. (学习自定义标签时使用它) *
④. session: 代表浏览器和服务器的一次会话, 是 HttpSession 的一个对象. 后面详细学习. *

⑤. application: 代表当前 WEB 应用. 是 ServletContext 对象. *
⑥. config: 当前 JSP 对应的 Servlet 的 ServletConfig 对象(几乎不使用). 若需要访问当前 JSP 配置的初始化参数, 
需要通过映射的地址才可以.

映射 JSP:
```xml
  <servlet>
  	<servlet-name>hellojsp</servlet-name>
  	<jsp-file>/hello.jsp</jsp-file>
  	<init-param>
  		<param-name>test</param-name>
  		<param-value>testValue</param-value>
  	</init-param>
  </servlet>
  
  <servlet-mapping>
  	<servlet-name>hellojsp</servlet-name>
	<url-pattern>/hellojsp</url-pattern>  	
  </servlet-mapping>
```
⑦. out: JspWriter 对象. 调用 out.println() 可以直接把字符串打印到浏览器上. *
⑧. page: 指向当前 JSP 对应的 Servlet 对象的引用, 但为 Object 类型, 只能调用 Object 类的方法(几乎不使用) 

⑨. exception: 在声明了 page 指令的 isErrorPage="true" 时, 才可以使用. *

`<%@ page isErrorPage="true" %>`

pageContext, request, session, application(对属性的作用域的范围从小到大)
out, response, config, page 
exception

6). JSP模版元素: JSP页面中的静态HTML内容称 

7). JSP表达式（expression）提供了将一个 java 变量或表达式的计算结果输出到客户端的简化方式，
它将要输出的变量或表达式直接封装在<%= 和 %>之中。
```jsp
<% 
	Date date = new Date();
	out.print(date);
%>

<%= date %>
```
8). JSP脚本片断（scriptlet）是指嵌套在<% 和 %>之中的一条或多条Java程序代码。 
多个脚本片断中的代码可以相互访问
```jsp
<% 
	String ageStr = request.getParameter("age");
	Integer age = Integer.parseInt(ageStr);
	
	if(age >= 18){
%>
		成人...
<%
	}else{
%>
		未成人...
<%
	}
%>
```
9). JSP 声明: JSP 声明将 Java 代码封装在<%！和 %>之中，它里面的代码将被插入进 Servle t的 _jspService 方法的外面
(在 JSP 页面中几乎从不这样使用)

10). JSP注释的格式：<%-- JSP 注释 --%> <!-- HTML 注释 -->
区别: JSP 注释可以阻止 Java 代码的执行. 
# 6. 和属性相关的方法:

1). 方法

void setAttribute(String name, Object o): 设置属性  
Object getAttribute(String name): 获取指定的属性

Enumeration getAttributeNames(): 获取所有的属性的名字组成的 Enumeration 对象
removeAttribute(String name): 移除指定的属性 

2). pageContext, request, session, application 对象都有这些方法!
这四个对象也称之为域对象. 

pageContext: 属性的作用范围仅限于当前 JSP 页面
request:  属性的作用范围仅限于同一个请求. 
session: 属性的作用范围限于一次会话: 浏览器打开直到关闭称之为一次会话(在此期间会话不失效)
application: 属性的作用范围限于当前 WEB 应用. 是范围最大的属性作用范围, 只要在一处设置属性, 在其他各处的 JSP 或 Servlet 中
都可以获取到. 
# 7. MVC 设计模式.
---
date: 2015-11-29 19:58
status: public
title: i_filter
---

# 1. Filter:

1). Filter 是什么 ?

①. JavaWEB 的一个重要组件, 可以对发送到 Servlet 的请求进行拦截, 并对响应也进行拦截. 
②. Filter 是实现了 Filter 接口的 Java 类.
③. Filter 需要在 web.xml 文件中进行配置和映射. 

2). 如何创建一个 Filter, 并把他跑起来

①. 创建一个 Filter 类: 实现 Filter 接口: public class HelloFilter implements Filter
②. 在 web.xml 文件中配置并映射该 Filter. 其中 url-pattern 指定该 Filter 可以拦截哪些资源, 即可以通过哪些 url 访问到该 Filter
```xml
<!-- 注册 Filter -->
<filter>
	<filter-name>helloFilter</filter-name>
	<filter-class>com.atguigu.javaweb.HelloFilter</filter-class>
</filter>

<!-- 映射 Filter -->
<filter-mapping>
	<filter-name>helloFilter</filter-name>
	<url-pattern>/test.jsp</url-pattern>
</filter-mapping>
```
3). Filter 相关的 API:

①. Filter 接口:

	> public void init(FilterConfig filterConfig): 类似于 Servlet 的 init 方法. 在创建 Filter 对象(Filter 对象在 Servlet 容器加载当前 WEB 应用时即被创建)后, 
	立即被调用, 且只被调用一次. 该方法用于对当前的 Filter 进行初始化操作. Filter 实例是单例的. 
	
		*  FilterConfig 类似于 ServletConfig
		
		* 可以在 web.xml 文件中配置当前 Filter 的初始化参数. 配置方式也和 Servlet 类似。
		
		<filter>
			<filter-name>helloFilter</filter-name>
			<filter-class>com.atguigu.javaweb.HelloFilter</filter-class>
			<init-param>
				<param-name>name</param-name>
				<param-value>root</param-value>
			</init-param>
		</filter>

	> public void doFilter(ServletRequest request, ServletResponse response,
			FilterChain chain): 真正 Filter 的逻辑代码需要编写在该方法中. 每次拦截都会调用该方法. 
			
		* FilterChain: Filter 链. 多个 Filter 可以构成一个 Filter 链. 	
		
			- doFilter(ServletRequest request, ServletResponse response): 把请求传给 Filter 链的下一个 Filter,
			若当前 Filter 是 Filter 链的最后一个 Filter, 将把请求给到目标 Serlvet(或 JSP)	
			
			- 多个 Filter 拦截的顺序和 <filter-mapping> 配置的顺序有关, 靠前的先被调用. 
			
	> public void destroy(): 释放当前 Filter 所占用的资源的方法. 在 Filter 被销毁之前被调用, 且只被调用一次. 
	
4). `<dispatcher>` 元素: 指定过滤器所拦截的资源被 Servlet 容器调用的方式，
可以是REQUEST,INCLUDE,FORWARD和ERROR之一，默认REQUEST. 
可以设置多个`<dispatcher>` 子元素用来指定 Filter 对资源的多种调用方式进行拦截

①. REQUEST：当用户直接访问页面时，Web容器将会调用过滤器。如果目标资源是通过RequestDispatcher的include()或forward()方法访问时，那么该过滤器就不会被调用。

通过 GET 或 POST 请求直接访问。 

②. FORWARD：如果目标资源是通过RequestDispatcher的forward()方法访问时，那么该过滤器将被调用，除此之外，该过滤器不会被调用。

或 `<jsp:forward page="/..." />` 或 通过 page 指令的 errorPage 转发页面. <%@ page errorPage="test.jsp" %>

②. INCLUDE：如果目标资源是通过RequestDispatcher的include()方法访问时，那么该过滤器将被调用。除此之外，该过滤器不会被调用。

或 `<jsp:include file="/..." />`

④. ERROR：如果目标资源是通过声明式异常处理机制调用时，那么该过滤器将被调用。除此之外，过滤器不会被调用。

在 web.xml 文件中通过 error-page 节点进行声明:
```xml
<error-page>
	<exception-type>java.lang.ArithmeticException</exception-type>
	<location>/test.jsp</location>
</error-page>

<filter-mapping>
	<filter-name>secondFilter</filter-name>
	<url-pattern>/test.jsp</url-pattern>
	<dispatcher>REQUEST</dispatcher>
	<dispatcher>FORWARD</dispatcher>
	<dispatcher>INCLUDE</dispatcher>
	<dispatcher>ERROR</dispatcher>
</filter-mapping>
```
	
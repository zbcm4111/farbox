---
date: 2015-11-29 19:50
status: public
title: g_自定义标签
---

# 1. 相对路径和绝对路径:

1). 为什么要解决相对路径的问题: 在有一个 Servlet 转发页面的情况下, 会导致相对路径的混乱. 
```jsp
a.jsp: <a href="ToBServlet">To B Page2</a>
ToBServlet: request.getRequestDispatcher("/dir/b.jsp").forward(request, response);
```
注意, 此时点击 To B Page2 超链接后的浏览器的地址栏的值: http://localhost:8989/day_36/ToBServlet, 实际显示的是
dir 路径下的 b.jsp

而 b.jsp 页面有一个超链接: <a href="c.jsp">TO C Page</a>. 默认情况下, c.jsp 应该和 b.jsp 在同一路径下. 此时点击超链接
将在浏览器地址栏显示: http://localhost:8989/day_36/c.jsp. 但在根目录下并没有 c.jsp, 所以会出现路径混乱的问题. 

2). 使用绝对路径会解决以上的问题:

绝对路径: 相对于当前 WEB 站点根目录的路径. 

http://localhost:8989/day_36/c.jsp: http://localhost:8989/ 是 WEB 站点的根目录, /day_36 是 contextPath,
/c.jsp 是相对于当前 WEB 应用的一个文件路径. 我们需要在当前 WEB 应用的任何的路径下都添加上 contextPath, 即可. 

比如: 
```
<a href="ToBServlet">To B Page2</a> 需改为: <a href="<%= request.getContextPath() %>/ToBServlet">To B Page2</a>
response.sendRedirect("a.jsp"); 需改为: response.sendRedirect(request.getContextPath() + "/a.jsp");
<form action="AddServlet"></form> 需改为: <form action="<%= request.getContextPath() %>/AddServlet"></form>
```
3). 在 JavaWEB 应用中 / 代表的是: 有时代表当前 WEB 应用的根目录, 有时代表的是站点的根目录.

/ 代表的是当前 WEB 应用的根路径: 若 / 所在的命令或方法需被 WEB 服务器解析, 而不是直接打给浏览器, 则 / 代表 WEB 应用的根路径. 此时编写
绝对路径就不需要在添加 contextPath 了. 
在 web.xml 文件中做 Serlvet 映射路径时,  
在请求转发: request.getRequestDispatcher("/dir/b.jsp").forward(request, response);

/ 代表的是站点的根目录: 若 / 直接交由浏览器解析, / 代表的就是站点的根路径, 此时必须加上 contextPath
```
<form action="/AddServlet"></form> 
response.sendRedirect("/a.jsp");
```
4). 如何获取 contextPath: 
ServletContext： getContextPath()
HttpServletRequest: getContextPath()

# 2. 自定义标签

1). HelloWorld

①. 创建一个标签处理器类: 实现 SimpleTag 接口. 
②. 在 WEB-INF 文件夹下新建一个 .tld(标签库描述文件) 为扩展名的 xml 文件. 并拷入固定的部分: 并对 
description, display-name, tlib-version, short-name, uri 做出修改
```xml
<taglib xmlns="http://java.sun.com/xml/ns/j2ee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-jsptaglibrary_2_0.xsd"
    version="2.0">
    
  <description>JSTL 1.1 core library</description>
  <display-name>JSTL core</display-name>
  <tlib-version>1.1</tlib-version>
  <short-name>c</short-name>
  <uri>http://java.sun.com/jsp/jstl/core</uri>
  
</taglib>
```
③. 在 tld 文件中描述自定义的标签:
```xml
<!-- 描述自定义的 HelloSimpleTag 标签 -->
  <tag>
  	<!-- 标签的名字: 在 JSP 页面上使用标签时的名字 -->
  	<name>hello</name>
  	
  	<!-- 标签所在的全类名 -->
  	<tag-class>com.atguigu.javaweb.tag.HelloSimpleTag</tag-class>
  	<!-- 标签体的类型 -->
  	<body-content>empty</body-content>
  </tag>
  ```
④. 在 JSP 页面上使用自定义标签: 

	> 使用 taglib 指令导入标签库描述文件: <%@taglib uri="http://www.atguigu.com/mytag/core" prefix="atguigu" %>
	
	> 使用自定义的标签: <atguigu:hello/> 
	
2). setJspContext: 一定会被 JSP 引擎所调用, 先于 doTag, 把代表 JSP 引擎的 pageContext 传给标签处理器类. 
```java
private PageContext pageContext;
	
@Override
public void setJspContext(JspContext arg0) {
	System.out.println(arg0 instanceof PageContext);  
	this.pageContext = (PageContext) arg0;
}	

3). 带属性的自定义标签:

①. 先在标签处理器类中定义 setter 方法. 建议把所有的属性类型都设置为 String 类型. 

private String value;
private String count;

public void setValue(String value) {
	this.value = value;
}

public void setCount(String count) {
	this.count = count;
}
```
②. 在 tld 描述文件中来描述属性:
```xml
<!-- 描述当前标签的属性 -->
<attribute>
	<!-- 属性名, 需和标签处理器类的 setter 方法定义的属性相同 -->
	<name>value</name>
	<!-- 该属性是否被必须 -->
	<required>true</required>
	<!-- rtexprvalue: runtime expression value 
		当前属性是否可以接受运行时表达式的动态值 -->
	<rtexprvalue>true</rtexprvalue>
</attribute>
```
③. 在页面中使用属性, 属性名同 tld 文件中定义的名字. 
```xml
<atguigu:hello value="${param.name }" count="10"/>
```
4). 通常情况下开发简单标签直接继承 SimpleTagSupport 就可以了. 可以直接调用其对应的 getter 方法得到对应的 API 
```java
public class SimpleTagSupport implements SimpleTag{
    
    public void doTag() 
        throws JspException, IOException{}
    
    private JspTag parentTag;
    
    public void setParent( JspTag parent ) {
        this.parentTag = parent;
    }
    
    public JspTag getParent() {
        return this.parentTag;
    }
    
    private JspContext jspContext;
    
    public void setJspContext( JspContext pc ) {
        this.jspContext = pc;
    }
    
    protected JspContext getJspContext() {
        return this.jspContext;
    }
    
    private JspFragment jspBody;
                
    public void setJspBody( JspFragment jspBody ) {
        this.jspBody = jspBody;
    }
    
    protected JspFragment getJspBody() {
        return this.jspBody;
    }   
}
```
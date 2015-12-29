---
date: 2015-11-29 19:54
status: public
title: h_jsp标签
---

# 1. 带标签体的自定义标签: 

1). 若一个标签有标签体: 

`<atguigu:testJspFragment>abcdefg</atguigu:testJspFragment>`

在自定义标签的标签处理器中使用 JspFragment 对象封装标签体信息. 

2). 若配置了标签含有标签体, 则 JSP 引擎会调用 setJspBody() 方法把 JspFragment 传递给标签处理器类
在 SimpleTagSupport 中还定义了一个 getJspBody() 方法, 用于返回 JspFragment 对象. 

3). JspFragment 的 invoke(Writer) 方法: 把标签体内容从 Writer 中输出, 若为 null, 
则等同于 invoke(getJspContext().getOut()), 即直接把标签体内容输出到页面上

有时, 可以 借助于 StringWriter, 可以在标签处理器类中先得到标签体的内容: 
```java
//1. 利用 StringWriter 得到标签体的内容.
StringWriter sw = new StringWriter();
bodyContent.invoke(sw);

//2. 把标签体的内容都变为大写
String content = sw.toString().toUpperCase();
```
4). 在 tld 文件中, 使用 body-content 节点来描述标签体的类型: 

`<body-content>`: 指定标签体的类型, 大部分情况下, 取值为 scriptless。可能取值有 3 种：
empty: 没有标签体	
scriptless: 标签体可以包含 el 表达式和 JSP 动作元素，但不能包含 JSP 的脚本元素
tagdependent: 表示标签体交由标签本身去解析处理。
若指定 tagdependent，在标签体中的所有代码都会原封不动的交给标签处理器，而不是将执行结果传递给标签处理器

`<body-content>tagdependent</body-content>`

5). 定义一个自定义标签: `<atguigu:printUpper time="10">abcdefg</atguigu>` 把标签体内容转换为大写, 并输出 time 次到
浏览器上. 

6). 实现 forEach 标签: 

	> 两个属性: items(集合类型, Collection), var(String 类型)
	
	> doTag: 
	
		* 遍历 items 对应的集合
		* 把正在遍历的对象放入到 pageContext 中, 键: var, 值: 正在遍历的对象. 
		* 把标签体的内容直接输出到页面上. 
```jsp
    <c:forEach items="${requestScope.customers }" var="cust2">
		${pageScope.cust2.id } -- ${cust2.name } <br>
	</c:forEach>

<atguigu:saveAsFile src="d:\\haha.txt">
	abcde
</atguigu>		
```
# 2. 开发有父标签的标签:

1). 父标签无法获取子标签的引用, 父标签仅把子标签作为标签体来使用. 

2). 子标签可以通过 getParent() 方法来获取父标签的引用(需继承 SimpleTagSupport 或自实现 SimpleTag 接口的该方法):
若子标签的确有父标签, JSP 引擎会把代表父标签的引用通过  setParent(JspTag parent)  赋给标签处理器

3). 注意: 父标签的类型是 JspTag 类型. 该接口是一个空接口, 但是来统一 SimpleTag 和 Tag 的. 实际使用需要进行类型的强制转换.

4). 在 tld 配置文件中, 无需为父标签有额外的配置. 但, 子标签是是以标签体的形式存在的, 所以父标签的` <body-content></body-content>`
需设置为 scriptless

5). 实现 
```jsp
<c:choose>
	<c:when test="${param.age > 24}">大学毕业</c:when>
	<c:when test="${param.age > 20}">高中毕业</c:when>
	<c:otherwise>高中以下...</c:otherwise>
</c:choose>
```
	> 开发 3 个标签: choose, when, otherwise
	> 其中 when 标签有一个 boolean 类型的属性: test
	> choose 是 when 和 otherwise 的父标签
	> when 在 otherwise 之前使用
	> 在父标签 choose 中定义一个 "全局" 的 boolean 类型的 flag: 用于判断子标签在满足条件的情况下是否执行. 
		* 若 when 的 test 为 true, 且 when 的父标签的 flag 也为 true, 则执行 when 的标签体(正常输出标签体的内容), 
		     同时把 flag 设置为 false
		* 若 when 的 test 为 true, 且 when 的父标签的 flag 为 false, 则不执行标签体. 
		* 若 flag 为 true, otherwise 执行标签体. 

# 3. JSTL:

1)*. c:out 主要用于对特殊字符进行转换. 真正进行输出时, 建议使用 c:out, 而不是使用 EL
2)*. c:set: 可以为域赋属性值。 而对域对象中的 JavaBean 的属性赋值用的并不多. 
3). c:remove: 移除指定域对象的指定属性值(较少使用, 即便移除也是在 Servlet 中完成)

4)*. c:if: 在页面上对现实的内容进行过滤, 把结果存储到域对象的属性中. 但不灵活, 会被其他的自定义标签所取代. 
5). c:choose, c:when, c:otherwise: 作用同上, 但麻烦, 不灵活.

6)*. c:forEach: 对集合进行遍历的. 常用!
7). c:forTokens: 处理字符串, 类似于 String 累的 split() 方法(知道即可)

8). c:import: 导入页面到当前页面的. (了解)
9). c:redirect: 当前页面进行重定向的. (使用较少)
10)*. c:url: 产生一个 URL 的, 可以进行 URL 重写, 变量值编码, 较为常用. 
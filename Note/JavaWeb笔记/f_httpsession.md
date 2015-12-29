---
date: 2015-11-29 18:15
status: public
title: f_httpsession
---

# 1. HttpSession 的生命周期：

1). 什么时候创建 HttpSession 对象
①. 对于 JSP: 是否浏览器访问服务端的任何一个 JSP, 服务器都会立即创建一个 HttpSession 对象呢？
不一定。

	> 若当前的 JSP 是客户端访问的当前 WEB 应用的第一个资源，且 JSP 的 page 指定的 session 属性值为 false, 则服务器就不会为 JSP 创建一个 HttpSession 对象;
	> 若当前 JSP 不是客户端访问的当前 WEB 应用的第一个资源，且其他页面已经创建一个 HttpSession 对象，
则服务器也不会为当前 JSP 页面创建一个 HttpSession 对象，而回会把和当前会话关联的那个 HttpSession 对象返回给当前的 JSP 页面.

②. 对于 Serlvet: 若 Serlvet 是客户端访问的第一个 WEB 应用的资源,
则只有调用了 request.getSession() 或 request.getSession(true) 才会创建 HttpSession 对象

2). page 指令的 session=“false“  到底表示什么意思？

	> 当前 JSP 页面禁用 session 隐含变量！但可以使用其他的显式的 HttpSession 对象
	
3). 在 Serlvet 中如何获取 HttpSession 对象？

	> request.getSession(boolean create): 
	create 为 false, 若没有和当前 JSP 页面关联的 HttpSession 对象, 则返回 null; 若有, 则返回 true	
	create 为 true, 一定返回一个 HttpSession 对象. 若没有和当前 JSP 页面关联的 HttpSession 对象, 则服务器创建一个新的
	HttpSession 对象返回, 若有, 直接返回关联的. 
	
	> request.getSession(): 等同于 request.getSession(true)

4). 什么时候销毁 HttpSession 对象:

①. 直接调用 HttpSession 的 invalidate() 方法: 该方法使 HttpSession 失效

②. 服务器卸载了当前 WEB 应用. 

③. 超出 HttpSession 的过期时间.

	> 设置 HttpSession 的过期时间: session.setMaxInactiveInterval(5); 单位为秒
	
	> 在 web.xml 文件中设置 HttpSession 的过期时间: 单位为 分钟. 
	
	<session-config>
        <session-timeout>30</session-timeout>
    </session-config>
    
④. 并不是关闭了浏览器就销毁了 HttpSession. 

# 2. 使用绝对路径：使用相对路径可能会有问题, 但使用绝对路径肯定没有问题. 

1). 绝对路径： 相对于当前 WEB 应用的路径. 在当前 WEB 应用的所有的路径前都添加 contextPath 即可. 

2). / 什么时候代表站点的根目录, 什么时候代表当前 WEB 应用的根目录

若 / 需要服务器进行内部解析, 则代表的就是 WEB 应用的根目录. 若是交给浏览器了, 则 / 代表的就是站点的根目录
若 / 代表的是 WEB 应用的根目录, 就不需要加上 contextPath 了. 

# 3. 表单的重复提交

1). 重复提交的情况: 
①. 在表单提交到一个 Servlet, 而 Servlet 又通过请求转发的方式响应一个 JSP(HTML) 页面, 
此时地址栏还保留着 Serlvet 的那个路径, 在响应页面点击 "刷新" 
②. 在响应页面没有到达时重复点击 "提交按钮". 
③. 点击 "返回", 再点击 "提交"

2). 不是重复提交的情况: 点击 "返回", "刷新" 原表单页面, 再 "提交"。

3). 如何避免表单的重复提交: 在表单中做一个标记, 提交到 Servlet 时, 检查标记是否存在且是否和预定义的标记一致, 若一致, 则受理请求,
并销毁标记, 若不一致或没有标记, 则直接响应提示信息: "重复提交" 

①. 仅提供一个隐藏域:` <input type="hidden" name="token" value="atguigu"/>`. 行不通: 没有方法清除固定的请求参数. 
②. 把标记放在 request 中. 行不通, 因为表单页面刷新后, request 已经被销毁, 再提交表单是一个新的 request.
③. 把标记放在 session 中. 可以！

	> 在原表单页面, 生成一个随机值 token
	> 在原表单页面, 把 token 值放入 session 属性中
	> 在原表单页面, 把 token 值放入到 隐藏域 中.

	> 在目标的 Servlet 中: 获取 session 和 隐藏域 中的 token 值
	> 比较两个值是否一致: 若一致, 受理请求, 且把 session 域中的 token 属性清除
	> 若不一致, 则直接响应提示页面: "重复提交"

# 4. 使用 HttpSession 实现验证码

1). 基本原理: 和表单重复提交一致:

	> 在原表单页面, 生成一个验证码的图片, 生成图片的同时, 需要把该图片中的字符串放入到 session 中. 
	> 在原表单页面, 定义一个文本域, 用于输入验证码. 

	> 在目标的 Servlet 中: 获取 session 和 表单域 中的 验证码的 值
	> 比较两个值是否一致: 若一致, 受理请求, 且把 session 域中的 验证码 属性清除
	> 若不一致, 则直接通过重定向的方式返回原表单页面, 并提示用户 "验证码错误"
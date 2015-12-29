---
date: 2015-11-29 20:02
status: public
title: j_warpper
---

# 1. 使用 Filter 完成一个简单的权限模型:

1). 需求: 

①. 管理权限
	> 查看某人的权限
	> 修改某人的权限
	
②. 对访问进行权限控制: 有权限则可以访问, 否则提示: 没有对应的权限, 请 返回

2). 实现:

②. 对访问进行权限控制:

	> 使用 Filter 进行权限的过滤: 检验用户是否有权限, 若有, 则直接响应目标页面; 若没有重定向到 403.jsp
	
	*   403.jsp
	
		<h4>
			没有对应的权限, 
			请 <a href="">返回</a>
		</h4>
		
	* 使用 Filter 如何进行过滤: 
	
		- 获取 servletPath, 类似于 /app_3/article1.jsp
		- 在用户已经登录(可使用 用户是否登录 的过滤器)的情况下, 获取用户信息. session.getAttribute("user")
		- 再获取用户所具有的权限的信息: List<Authority>
		- 检验用户是否有请求  servletPath 的权限: 可以思考除了遍历以外, 有没有更好的实现方式
		- 若有权限则: 响应
		- 若没有权限: 重定向到 403.jsp 
		
	* others: 
		- 用户若登录, 需要把用户信息(User 对象)放入到 HttpSession 中.
		- 在检验权限之前, 需要判断用户是否已经登录.  	

①. 管理权限:

	> 封装权限信息: Authority
```java
	Authority{
		//显示到页面上的权限的名字
		private String displayName;
		
		//权限对应的 URL 地址: 已权限对应着一个 URL, 例如 Article_1 -> /app_4/article1.jsp
		private String url;
	}
	```
	> 封装用户信息: User
```java
	User{
		private String username;
		private List<Autority> authorities;
		
		//...
	}
```
	> 创建一个 UserDao:
```java
	User get(String username);
	void update(String username, List<Autority>);
```
	> 页面
	
	authority-manager.jsp: 
	
		* 有一个 text 文本框, 供输入 username, 提交后, 使用 checkbox 显示当前用户所有的权限的信息.
```jsp
		<form action="/day_40/AuthorityServlet?method=get" method="post">
			Name: <input name="name" type="text"/>
			<input type="submit" value="Submit"/>
		</form>
		
```		
		* 检查 request 中是否有 user 信息, 若有, 则显示 
		xxx 的权限为: 对应的权限的 checkbox 打上对号. 提示, 页面上需要通过两层循环的方式来筛选出被选择的权限. 
```jsp
		<form action="/day_40/AuthorityServlet?method=get" method="post">
	
			Name: <input name="name" type="text"/>
			<input type="submit" value="Submit"/>
		
		</form>
		
		<br><br>
		
		AAA 的权限是:
		
		<br><br>
		
		<form action="/day_40/AuthorityServlet?method=update" method="post">
			
			<!-- 使用隐藏域来保存用户的 name -->
			<input name="name" type="hidden" value="AAA"/>
				
			<input type="checkbox" name="authority" value="/app_4/article1.jsp" 
				checked="checked"/>Article_1
			<br><br>
			
			<input type="checkbox" name="authority" value="/app_4/article2.jsp" 
				checked="checked"/>Article_2
			<br><br>
			
			<input type="checkbox" name="authority" value="/app_4/article3.jsp" 
				checked="checked"/>Article_3
			<br><br>
			
			<input type="checkbox" name="authority" value="/app_4/article4.jsp" />Article_4
			<br><br>
				
			<input type="submit" value="Submit"/>
		
		</form> 
```
	> Servlet
	
	authority-manager.jsp 提交表单后 get 方法: 获取表单的请求参数: username, 再根据 username 获取 User 信息. 把 user 放入到
	request 中, 转发到 authority-manager.jsp.
	
	authority-manager.jsp 修改权限的表单提交后 update 方法: 获取请求参数: username, authory(多选); 把选项封装为 List; 调用
	UserDao 的 update() 方法实现权限的修改; 重定向到 authority-manager.jsp



#  2. HttpServletWrapper 和 HttpServletResponseWrapper

1). Servlet API 中提供了一个 HttpServletRequestWrapper 类来包装原始的 request 对象,
HttpServletRequestWrapper 类实现了 HttpServletRequest 接口中的所有方法, 
这些方法的内部实现都是仅仅调用了一下所包装的的 request 对象的对应方法
```java
//包装类实现 ServletRequest 接口. 
public class ServletRequestWrapper implements ServletRequest {

    //被包装的那个 ServletRequest 对象
    private ServletRequest request;
	
	//构造器传入 ServletRequest 实现类对象
    public ServletRequestWrapper(ServletRequest request) {
		if (request == null) {
		    throw new IllegalArgumentException("Request cannot be null");   
		}
		this.request = request;
    }

	//具体实现 ServletRequest 的方法: 调用被包装的那个成员变量的方法实现。 
    public Object getAttribute(String name) {
		return this.request.getAttribute(name);
	}

    public Enumeration getAttributeNames() {
		return this.request.getAttributeNames();
	}    
	
	//...	
}	
```
相类似 Servlet API 也提供了一个 HttpServletResponseWrapper 类来包装原始的 response 对象

2). 作用: 用于对 HttpServletRequest 或 HttpServletResponse 的某一个方法进行修改或增强.
```java
public class MyHttpServletRequest extends HttpServletRequestWrapper{

	public MyHttpServletRequest(HttpServletRequest request) {
		super(request);
	}
	
	@Override
	public String getParameter(String name) {
		String val = super.getParameter(name);
		if(val != null && val.contains(" fuck ")){
			val = val.replace("fuck", "****");
		}
		return val;
	}
}
```
3). 使用: 在 Filter 中, 利用 MyHttpServletRequest 替换传入的 HttpServletRequest
```java
HttpServletRequest req = new MyHttpServletRequest(request);
filterChain.doFilter(req, response);
```
此时到达目标 Servlet 或 JSP 的 HttpServletRequest 实际上是 MyHttpServletRequest
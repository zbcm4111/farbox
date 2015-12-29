maximo+体系架构
http://blog.sina.com.cn/s/blog_98a617c70101ur7x.html

maximo 项目指导
http://www.blogjava.net/cpegtop/articles/372899.html

maximo eclipse插件
http://maximodeveloper.sourceforge.net/


# jar/war/ear文件的区别
jar/war/ear三种文件，在文件结构上，三者并没有什么不同，它们都采用zip或jar档案文件压缩格式。但是它们的使用目的有所区别：
* Jar文件（扩展名为. Jar）包含Java类的普通库、资源（resources）、辅助文件 （auxiliary files）等
* War文件（扩展名为.War）包含全部Web应用程序。在这种情形下，一个Web应用程序被定义为单独的 一组文件、类和资源，用户可以对jar文件进行封装，并把它作为小型服务程序（servlet）来访问。
* Ear文件（扩展名为.Ear）包含全部企业应用程序。在这种情形下，一个企业应用程序被定义为多个jar 文件、资源、类和Web应用程序的集合。
每一种文件（.jar, .war, .ear）只能分别由应用服务器（application servers）、小型服务程序容器（servlet containers）、EJB容器（EJB containers）等进行处理。
* JAR：Java   Archive   file ; 

* WAR：Web   Archive   file 

* EAR  ：Enterprise   Archive   file 

jar:包含内容 class、properties文件，是文件封装的最小单元;

war:Servlet、JSP页面、JSP标记库、JAR库文件、HTML/XML文档和其他公用资源文件，如图片、音频文件等;

ear:除了包含JAR、WAR以外，还包括EJB组件  

部署文件 application-client.xml 	web.xml 	application.xml  
级别 	 小				 			中 			大  

jar:封装类  

war:封装web站点   

ear:可以封装ejb 

# EJB


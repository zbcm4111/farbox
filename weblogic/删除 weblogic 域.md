我的weblogic主目录路径为：D:\Oracle\Middleware，具体路径根据个人安装路径而定，我现在想删除域名为base_domain的域，具体操作步骤如下：

1、删除 D:\Oracle\Middleware\wlserver_10.3\common\nodemanager\nodemanager.domains 里的base_domain域内容

```
#Domains and directories created by Configuration Wizard
#Tue Dec 17 11:28:34 GMT+08:00 2013
base_domain=D\:\\Oracle\\Middleware\\user_projects\\domains\\base_domain
test_domain=D\:\\Oracle\\Middleware\\user_projects\\domains\\test_domain
```

文件内容大致如上，删除base_domain这一行内容

2、删除 D:\Oracle\Middleware\domain-registry.xml 里的base_domain域内容

<?xml version="1.0" encoding="UTF-8"?>
<domain-registry xmlns="http://xmlns.oracle.com/weblogic/domain-registry">
  <domain location="D:\Oracle\Middleware\user_projects\domains\test_domain"/>
  <domain location="D:\Oracle\Middleware\user_projects\domains\base_domain"/>
</domain-registry>

文件内容大致如上，删除base_domain这一行内容


3、删除 D:\Oracle\Middleware\user_projects\applications 下的域目录（删除base_domain整个文件夹）
4、删除 D:\Oracle\Middleware\user_projects\domains 下的域目录（删除base_domain整个文件夹）
5、删除开始菜单中welogic下的域目录（删除base_domain整个文件夹）
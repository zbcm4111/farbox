# 集群
集群的优点：
load balance负载均衡
fail over失败迁移

例如多个tomcat连接同一个数据库，其中一个tomcat无法连接数据库交给其它的处理。


Oracle体系结构
实例（内存中）  数据库 （硬盘上）

# CMD中的一些命令
sqlplus maximo/maximo@loaclhost:1521/orcl CMD中登录命令

登录后：
spool d:\基本查询.txt   
spool off
录屏命令

host cls  清屏(liunx下host clear)
show user 显示当前用户
select * from tab 显示当前用户下的表和视图

desc tablename 显示tablename 的表结构

# 其它一些知识
isqlplus服务，即命令行的网页版，通过http://localhost:5560/isqlplus/访问，有些版本中例如11g，没有这个功能，因为不安全。

oracle中**字符串**大小写敏感

修改日期格式：
select * from v$nls_parameters;
alter session set NLS_DATE_FORMAT='yyyy-mm-dd';

oracle 自动开启事务，mysql 要手动开启


# 查询

连接符 ||
select name||'的工资是'||sal from emp;

选择和表没关系的数据时可以用dual表，伪表
select 'hello'||' world' from dual;

空值永远不等于空值，空值不是0
nvl(a,b)	虑空函数
nvl2

列的别名 name as "名字"  as可以省略，如果没有关键字、空格，引号也可以省略

distinct去重
select distinct name，job from emp 
distinct作用与后面所有的列，如果后面的组合起来没有重复的，就不会去除

order by排序
asc顺序，desc倒序
order by sal，name  先根据sal排，sal一样的再根据name排
order by sal desc，name desc  desc只作用于最近的那一列

# sql优化：
尽量使用列名代替*
where条件是从右往左判断的

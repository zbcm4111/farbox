# ��Ⱥ
��Ⱥ���ŵ㣺
load balance���ؾ���
fail overʧ��Ǩ��

������tomcat����ͬһ�����ݿ⣬����һ��tomcat�޷��������ݿ⽻�������Ĵ���


Oracle��ϵ�ṹ
ʵ�����ڴ��У�  ���ݿ� ��Ӳ���ϣ�

# CMD�е�һЩ����
sqlplus maximo/maximo@loaclhost:1521/orcl CMD�е�¼����

��¼��
spool d:\������ѯ.txt   
spool off
¼������

host cls  ����(liunx��host clear)
show user ��ʾ��ǰ�û�
select * from tab ��ʾ��ǰ�û��µı����ͼ

desc tablename ��ʾtablename �ı�ṹ

# ����һЩ֪ʶ
isqlplus���񣬼������е���ҳ�棬ͨ��http://localhost:5560/isqlplus/���ʣ���Щ�汾������11g��û��������ܣ���Ϊ����ȫ��

oracle��**�ַ���**��Сд����

�޸����ڸ�ʽ��
select * from v$nls_parameters;
alter session set NLS_DATE_FORMAT='yyyy-mm-dd';

oracle �Զ���������mysql Ҫ�ֶ�����


# ��ѯ

���ӷ� ||
select name||'�Ĺ�����'||sal from emp;

ѡ��ͱ�û��ϵ������ʱ������dual��α��
select 'hello'||' world' from dual;

��ֵ��Զ�����ڿ�ֵ����ֵ����0
nvl(a,b)	�ǿպ���
nvl2

�еı��� name as "����"  as����ʡ�ԣ����û�йؼ��֡��ո�����Ҳ����ʡ��

distinctȥ��
select distinct name��job from emp 
distinct������������е��У����������������û���ظ��ģ��Ͳ���ȥ��

order by����
asc˳��desc����
order by sal��name  �ȸ���sal�ţ�salһ�����ٸ���name��
order by sal desc��name desc  descֻ�������������һ��

# sql�Ż���
����ʹ����������*
where�����Ǵ��������жϵ�

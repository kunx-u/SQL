# Day 01

## Oracle = 数据库

sqlplus = 让我们和数据库进行沟通的工具

services.msc = 服务

control = 控制面板

regedit = 注册表



## SQL = Structured Query Language = 结构化查询语言

### DDL = Data Definition Lanaguage = 数据定义语言

create 创建  alter 修改  drop 删除  truncate 截断

#### DML = Data Manipulation Language = 数据操纵语言

insert 新增  delete 删除  update 更新

#### DQL = Data Query Language = 数据查询语言

select 查询

#### DCL = Data Control Language = 数据控制语言

grant 授权  revoke 取消授权

#### TCL = Transaction Control Language = 事务控制语言

commit 提交  rollback 回滚  savepoint 保存点



### 用户名：system,scott

| 用户操作语句     |                                           |
| ---------------- | ----------------------------------------- |
| 连接用户：       | conn 用户名/密码;<br />conn system/etoak; |
| 查看当前用户：   | show user;                                |
| 给用户解锁：     | alter user scott account unlock;          |
| 给用户加锁：     | alter user scott account lock;            |
| 给用户授权：     | grant dba to scott;                       |
| 给用户取消授权： | revoke dba from ET2406;                   |
| 创建新用户：     | create user ET2406 identified by etoak;   |
| 给用户修改密码： | alter user scott identified by etoak;     |
| 删除用户：       | drop user ET2406;                         |

练习：在SCOTT用户下新建用户ET，在ET用户下新建用户ET2406，连接ET2406，删除ET2406



### Oracle的数据类型：

#### 字符型：

varchar2(20)	可变类型	0-4000字节    

char(4)		   固定类型      0-2000字节

#### 数值型：

number(5)

number(5,1)  

#### 日期型：

date

timestamp  包含毫秒数



### 建表：

~~~sql
create table student(
	id number(5),
    name varchar2(20),
    birthday date,
    sal number(5,1)
);
~~~

| 表操作语句               |                                                      |
| ------------------------ | ---------------------------------------------------- |
| 查看表结构：             | desc student;                                        |
| 查看当前用户下有哪些表： | select table_name from user_tables;                  |
| 建表以后，修改表名：     | alter table student rename to stu;                   |
| 建表以后，新增字段：     | alter table student add email varchar2(20);          |
| 建表以后，修改列名：     | alter table student rename column email to youxiang; |
| 建表以后，删除字段：     | alter table student drop column youxiang;            |
| 建表以后，修改数据类型： | alter table student modify name varchar2(25);        |
| 建表以后，删除表：       | drop table student2;                                 |
| 建表以后，截断表：       | truncate table student;                              |

练习：

1.新建一张teacher表，字段有老师工号，老师姓名，老师入职时间，老师年薪(整数位五位，小数位两位)

2.将teacher表名修改为tea，新增字段hobby，将hobby修改为aihao，删除aihao，截断表，删除表

### 增删改查：

| 增删改查         |                                                              |
| ---------------- | ------------------------------------------------------------ |
| 新增数据：insert | insert into student values(1,'张三',sysdate,5000);<br />insert into student(id,name,sal) values(2,'李四',6000); |
| 删除数据：delete | delete from student;<br />delete from student where id = 1;  |
| 修改数据：update | update student set sal = sal + 500;<br />update student set sal = sal + 500 where id = 2;<br />update student set sal = sal + 500,name = 'zsf' where id = 1; |
| 查询数据：select | select * from student;<br />select name,sal from student;<br />select name,sal from student where name = '张三';<br />select name,sal from student where name = '张三' and id = 1; |

~~~sql
条件：
and			并且，两个条件必须都成立
or			或者，有一个条件成立即可
between and	闭合区间

select name,sal from student where sal between 5000 and 5500;
select name,sal from student where sal >= 5000 and sal <= 5500;

比较：
>  <  >=  <=  !=
<>  不等于

运算：
+ - * / 
~~~

### 模糊查询：like

~~~sql
%：代表任意位的任意字符
_：代表一位上的任意字符

SELECT name FROM student where name like 'ET%';
SELECT name FROM student where name like 'ET_%';
SELECT name FROM student where name like '%s%';

not like：
SELECT name FROM student where name not like '%s%';
~~~

#### 逃离符：escape

~~~sql
通过指定一个字符位进行逃离，来保证like之后的特殊字符看作是普通字符

SELECT name FROM student where name like 'ET,_' escape ',';
SELECT name FROM student where name like 'ET._.%' escape '.';
~~~

练习：

1.新增张三，李四，王五，张柳

2.给姓张的和姓李的工资涨30%

3.用查询的方式展示出第二题的效果

#### 别名：

~~~sql
select name,sal * 1.3 as sal from student where name like '张%' or name like '李%';
select s.name sname,s.sal ssal from student s;
~~~

### 分组：group by

~~~sql
根据某个列或者多个列上相同的值划分为一个组，那么这个表就被分为多个组

根据字段A分组，只能查询字段A，其他字段需要以组函数的形式出现
~~~

~~~sql
select deptno from emp group by deptno;
select deptno,job from emp group by deptno,job;
select deptno,count(ename) num from emp group by deptno;
~~~

#### 条件：having

~~~sql
select deptno,count(ename) num from emp 
group by deptno having count(ename) >= 5;
~~~

### 去重：distinct

~~~sql
支持单列，多列的去重

select distinct deptno from emp;
select distinct deptno,job from emp;
~~~

### 排序：order by

升序：asc   降序：desc

~~~sql
select deptno from emp order by deptno;  -- 默认升序
select deptno from emp order by deptno asc;
select deptno,sal from emp order by deptno asc,sal desc;
~~~

## 查询关键字的优先级：

~~~sql
select		列名	-- 优先级高于order by	-- （嵌套查询）
from		表名	-- 优先级最高			-- （嵌套查询）
-- 连表查询		-- （嵌套查询）
where		条件	-- 优先级次高			-- （嵌套查询）
group by	分组	-- 优先级次于where
having		条件	-- 优先级一定在group by之后-- （嵌套查询）
order by	排序	-- 优先级最低

select deptno,count(deptno) num
from emp
where job = 'SALESMAN'
group by deptno
having count(deptno) >= 2
order by num desc;
~~~

### is null：是空

### is not null：非空

~~~sql
select birthday from student where birthday is not null;
~~~

### 函数：

| 聚组函数：                                                   |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| max()：求最大值<br />min()：求最小值<br />sum()：求和<br />avg()：求平均值 | select max(sal) maxsal,min(sal) minsal,sum(sal) sumsal,avg(sal) avgsal from emp; |
| count()：求个数                                              | select count(*) num from student;<br/>select count(birthday) num from student;<br/>select count(1) num from student; |
| **单行函数：**                                               |                                                              |
| ceil()：向上取整<br />floor()：向下取整                      | select ceil(12.3) num from dual;                             |
| abs()：求绝对值                                              | select abs(-666) num from dual;                              |
| power(a,b)：求a的b次方                                       | select power(2,3) num from dual;                             |
| sqrt()：求正平方根                                           | select sqrt(16) num from dual;                               |
| sign()：求符号位<br />正数：1  负数：-1  零：0               | select sign(-666) num from dual;<br/>select sign(666) num from dual;<br/>select sign(0) num from dual; |
| round()：求四舍五入                                          | select round(3.141592654) num from dual;<br/>select round(3.141592654,3) num from dual; |
| trunc()：求直接截断                                          | select trunc(3.141592654) num from dual;<br/>select trunc(3.141592654,3) num from dual; |

# Day 03

## 伪列：

### rownum：行号

### rowid：数据库会给每条数据添加物理地址唯一标识

~~~sql
适用于删除完全重复的数据

delete from lyric where rowid not in
(select min(rowid) from lyric group by content);
~~~

### in：表示条件可以是查询结果中的任意一个值

~~~sql
字段 in (值1,值2...)
相当于：
字段 = 值1 or 字段 = 值2 or ...
~~~

~~~sql
select id,name from school where id in (select sid from student);
-- 一条没意义的语句
select id,name from school where (id,name) in (select sid,name from student);
~~~

### not in：表示条件不能是查询结果中的任意一个值

~~~sql
字段 not in (值1,值2...)
相当于：
字段 <> 值1 and 字段 <> 值2 and ... 
~~~

~~~sql
select id,name from school where id not in (select sid from student);
~~~

练习：

--35列出工资等于30号部门中某个员工工资的所有员工的姓名和工资。

-- 删除表中5-10条数据

### some/any：用法和IN相同，IN用在无符号的情况，some/any用在有符号的情况

~~~sql
select id,name from school where id = some (select sid from student);

select id,name from school where id > any (select sid from student);
~~~

### all：比所有值都大，或者比所有值都小

~~~sql
select id,name from school where id >= all (select sid from student);
~~~

~~~ sql
***：
> any  	> min
< any  	< max
> all  	> max
< all  	< min
~~~

### exists：存在

### not exists：不存在

~~~sql
select id,name from school where exists (select sid from student where student.sid = school.id);
~~~

### 联合关键字：

#### union：结果唯一，并且按照默认顺序排序

#### union all：结果不唯一

~~~sql
select id,name from school where name like '山东%'
union
select id,name from school where name like '%大学';

select id,name from school where name like '山东%'
union all
select id,name from school where name like '%大学';
~~~

#### intersect：求交集

~~~sql
select id,name from school where name like '山东%'
intersect
select id,name from school where name like '%大学';
~~~

#### minus：从第一个结果集中减去第二个结果集中重复的数据

~~~sql
select id,name from school where name like '山东%'
minus
select id,name from school where name like '%大学';
~~~

### 复制表：

~~~sql
1.复制表结构和表数据
create table emp2 as select * from emp;
2.复制表结构
create table emp3 as select * from emp where 1 = 2;
3.复制指定列的数据
create table emp4 as select empno,ename from emp;
4.在新表里给列重命名
create table emp5(id,name) as select empno,ename from emp;
5.一次性添加多条数据
insert into emp3 select * from emp;
~~~

### 视图：view

~~~sql
假表，将查询结果保存在数据库中，下次查询不用编译，可以直接从数据库中获取
提高查询效率，避免没有权限的用户查看隐私字段
简单视图数据可以动态来源于原表，复杂视图需要手动编译(alter view 视图名 compile;)
经过函数修饰的列一定要起别名，列名不能重复

创建视图：
create view 视图名 as 查询数据;
create view v_name as
select student.name stuname,school.name schname from student
left join school on student.sid = school.id;

select stuname,schname from v_name;

删除视图：
drop view v_name;
~~~

### 序列：sequence

~~~sql
Oracle通过调用序列实现主键自增，一个有序的整数列值
调用序列的下一个值，才能查看序列的当前值，调用序列的下一个值会作为下一次调用的初始值

创建序列：
create sequence seq_test
start with 1		-- 从1开始
increment by 1		-- 一次增长1
minvalue 1			-- 最小值
maxvalue 100		-- 最大值
nocycle				-- 不循环
nocache;			-- 不缓存
~~~

~~~sql
1.消除延迟段创建特性
alter system set deferred_segment_creation = false;
2.创建表
create table test2(id number(5),name varchar2(20));
3.创建序列
create sequence seq_test2;
4.新增数据
insert into test2 values(seq_test2.nextval,'xiangjiao');
insert into test2 values(seq_test2.nextval,'火龙果');
5.查询数据
select * from test2;

查看序列的当前值：
select seq_test.currval from dual;
查看序列的下一个值：
select seq_test.nextval from dual;

删除序列：
drop sequence seq_test;
~~~

### 索引：index

~~~sql
数据库会在具有唯一约束的列上自动添加唯一索引

创建索引：
create index 索引名 on 表名(列名);
create index ind_name on student(name);
~~~

#### 索引分类：

~~~sql
普通索引：normal
create index 索引名 on 表名(列名);
create index ind_name on student(name);
create index ind_lengname on school(length(name));

唯一索引：unique
create unique index 索引名 on 表名(列名);
create unique index ind_sid on student(sid);

位图索引（分类索引）：bitmap
数据量大，基数小的列
create bitmap index 索引名 on 表名(列名);
create bitmap index ind_cid on student(cid);
~~~

什么情况会使索引失效？

~~~sql
1使用like关键字模糊查询时，% 放在前面索引不起作用，只有“%”不在第一个位置，索引才会生效（like ‘%文’–索引不起作用）
2使用联合索引时，只有查询条件中使用了这些字段中的第一个字段，索引才会生效
*:Oracle添加联合索引：CREATE INDEX idx_emp ON emp(ename,sal);
*:Mysql添加联合索引：ALTER TABLE employee ADD INDEX idx_emp (ename, salary);
3使用OR关键字的查询，查询语句的查询条件中只有OR关键字，且OR前后的两个条件中的列都是索引时，索引才会生效，否则索引不生效。
4尽量避免在where子句中使用!=或<>操作符，否则引擎将放弃使用索引而进行全表扫描。
5对查询进行优化，应尽量避免全表扫描，首先应考虑在where以及order by涉及的列上建立索引。
6应尽量避免在 where 子句中对字段进行表达式操作，这将导致引擎放弃使用索引而进行全表扫描。如：
　　select id from t where num/2=100
　　应改为:
　　select id from t where num=100*2
7尽量避免在where子句中对字段进行函数操作,将导致引擎放弃使用索引而进行全表扫描。
8不要在 where 子句中的“=”左边进行函数、算术运算或其他表达式运算，否则系统将可能无法正确使用索引。
9并不是所有的索引对查询都有效，sql是根据表中的数据来进行查询优化的，当索引列有大量数据重复时，sql查询不会去利用索引。
10索引并不是越多越好，索引固然可以提高相应的 select 的效率，但同时也降低了 insert 及 update 的效率，
　　因为 insert 或 update 时有可能会重建索引，所以怎样建索引需要慎重考虑，视具体情况而定。一个表的索引数最好不要超过6个，若太多则应考虑一些不常使用到的列上建的索引是否有必要。　
~~~

### 表空间：tablespace

在一段内存中多数存储的是表，所以称为表空间

创建表空间：

~~~sql
create tablespace table_user   -- 表空间的名字
datafile 'D:/table_user.dbf'   -- 表空间的位置
size 5M                        -- 初始大小
autoextend on next 5M          -- 下一次自动拓展多少
maxsize 100M;                  -- 最大值
~~~

创建用户并给分配默认的表空间：

~~~sql
create user ET identified by etoak default tablespace table_user;
~~~

创建用户未指定表空间：

~~~sql
create user ET2 identified by etoak;
~~~

给用户修改表空间：

~~~sql
alter user ET2 default tablespace table_user;
~~~

删除表空间：
如果删除用户指定的表空间，该用户仍指向原表空间位置，需要手动指定一个有效的表空间位置

~~~sql
drop tablespace table_user including contents and datafiles;
~~~

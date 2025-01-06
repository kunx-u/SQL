# Day 02

## 函数：

| 字符函数：                                                   |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| upper()：转换成大写<br />lower()：转换成小写<br />initcap()：首字母大写<br />length()：求长度 | select name,upper(name) uname,lower(name) lname,initcap(name) iname,length(name) lenname from student; |
| substr(a1,a2,a3)：截取字符串<br />a1：原字符串<br />a2：从哪开始截取<br />a3：截取长度 | select substr('woshizhizhuxia',3,9) str from dual;<br/>select substr('woshizhizhuxia',3) str from dual; |
| replace(a1,a2,a3)：替换字符串<br />a1：原字符串<br />a2：要替换的字符<br />a3：替换成的字符 | select replace('woshizhizhuxia','h','0000') str from dual;<br/>select replace('woshizhizhuxia','h') str from dual; |
| instr(a1,a2,a3,a4)：索引字符串<br />a1：原字符串<br />a2：想要找到的字符<br />a3：从哪开始找<br />a4：第几次出现 | select instr('woshizhizhuxia','h',5,2) num from dual;<br/>select instr('woshizhizhuxia','h',5) num from dual;<br/>select instr('woshizhizhuxia','h') num from dual; |
| concat(a1,a2)：拼接字符串                                    | select concat(name,sal) str from student;<br/>select concat(concat(name,birthday),sal) str from student; |
| lpad(a1,a2,a3)：左侧补全<br />rpad(a1,a2,a3)：右侧补全<br />a1：原字符串<br />a2：补全到多少位<br />a3：用什么来补全 | select phone,lpad(phone,11,'138') phoneno from student;      |
| trim()：默认去除两侧空格<br />trim(a1 from a2)：把a2的两侧去除a1 | select trim('a' from 'aaababaaa') str from dual;             |
| ltrim()：左侧去除<br />rtrim()：右侧去除                     | select ltrim('    abc',' ') str from dual;<br/>select ltrim('    abc') str from dual;<br/>select ltrim('    abc','a') str from dual; |

练习：

1.新增字段phone，更新数据15556785678，18888888888，15889655698

2.中奖号码如：155****5678，188****8888，158****5698

~~~sql
日期：
两个日期可以相减，单位是天

sqlplus中修改日期展示格式：
alter session set nls_date_format = 'yyyy-mm-dd hh24:mi:ss';
永久修改方法：详见环境变量

yyyy	年 	year
mm		月	month  带'月'的月份
ddd		日	年中的日
dd		日	月中的日
d		日	周中的日
hh24	24小时制
hh12	12小时制
mi		分
ss		秒
xff		毫秒
ff3		毫秒保留三位
~~~

| 日期函数                                   |                                                              |
| ------------------------------------------ | ------------------------------------------------------------ |
| add_months()：在某个日期上添加多少个月     | select add_months(sysdate,4) time from dual;                 |
| months_between()：两个日期之间存在多少个月 | select months_between(sysdate,to_date('20240715121212','yyyy-mm-dd hh24:mi:ss')) time from dual; |
| next_day()：下一个周几是哪天               | select next_day(sysdate,'星期二') time from dual;            |
| last_day()：给定日期所在月份的最后一天     | select last_day(sysdate) time from dual;                     |

练习：

--10查询各月倒数第3天入职的员工信息

| 转换函数：                                                   |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| to_number()：将字符类型的数值转换成数值类型                  | select phone from student where to_number(phone) = 520;      |
| to_char()：<br />1）将数值类型转换成字符类型<br />2）将日期类型转换成字符类型<br />3）格式化字符串 | select sal from student where to_char(sal) = '5000';<br />select to_char(sysdate,'yyyy-mm-dd hh24:mi:ss') time from dual;<br/>select to_char(systimestamp,'yyyy-mm-dd hh24:mi:ss:ff3') time from dual;<br />select to_char('1000000','999,999,999,999.99') money from dual; |
| to_date()：将字符类型的日期转换成日期类型                    | select to_date('20240816112121','yyyy-mm-dd hh24:mi:ss') time from dual; |
| **通用函数：**                                               |                                                              |
| nvl(原字符串,是空展示什么)：空值处理                         | select sal,comm,sal+nvl(comm,0) salary from emp;             |
| nvl2(原字符串,不是空展示什么,是空展示什么)：空值处理二代     | select sal,comm,sal+nvl2(comm,comm,0) salary from emp;       |

练习：

有一个字符串'20240816113737'，取出年，取出月，取出日

year  month  day

2024     08       16

### decode(c1,c2,c3,c4,c5...Cx,Cx+1)：

~~~sql
c1是原字符串，从c2开始每两个参数看作是一组，拿每组的第一个参数和c1进行比较，如果相同则返回该组的第二个参数

相当于：
第一次比较：c2 == c1 ?  c3 :
第二次比较：c4 == c1 ?  c5 :

如果参数个数是奇数个，并且最终判断没有相同的值，则返回空
如果参数个数是偶数个，并且最终判断没有相同的值，则返回最后一个参数的值
~~~

*：如果部门编号是10，则工资涨五百，如果部门编号是20，则工资减五百，其他部门加二百

~~~sql
select ename,deptno,sal,decode(deptno,10,sal + 500,20,sal - 500,sal + 200) salary
from emp;
~~~

*：如果部门编号是10，则工资涨五百，如果部门编号是20，则工资减五百

~~~sql
select ename,deptno,sal,decode(deptno,10,sal + 500,20,sal - 500) salary
from emp;
~~~

### (case when then else end)：条件取值语句

~~~sql
(case -- 拿来做比较的值
 when -- 如果..
 then -- 则..
 else -- 否则..
 end) -- 结束
~~~

*：如果部门编号是10，则工资涨五百，如果部门编号是20，则工资减五百，其他部门加二百

~~~sql
select ename,deptno,sal,(case deptno when 10 then sal + 500 when 20 then sal - 500 else sal + 200 end) salary from emp;
~~~

*：如果部门编号是10，则工资涨五百，如果部门编号是20，则工资减五百

~~~sql
select ename,deptno,sal,(case deptno when 10 then sal + 500 when 20 then sal - 500 end) salary from emp;
~~~

练习：

如果工种为'SALESMAN'，则工资减三百，如果工种为'CLERK'，则工资加三百，其他部门加一百



### 约束：constraint

#### 主键约束：primary key

~~~sql
主键：在一张表中，能够唯一定位一条数据的列，称为主键列
*：非空且唯一

建表后添加主键：
create table test(id number(5),name varchar2(20));
alter table test add constraint zj primary key(id);

建表时添加主键：
create table test2(
id number(5) primary key,
name varchar2(20)
);
~~~

#### 外键约束：foreign key   *：references

~~~sql
外键：在子表中有一个列引用了父表中的主键列，那么这个列在子表中就被称为外键
*：一张表可以有多个外键
~~~

|      |   表名   |  主键  |     外键      |                                                          |
| :--: | :------: | :----: | :-----------: | :------------------------------------------------------: |
| 1）  |   emp    | empno  |    deptno     |                           子表                           |
|      |   dept   | deptno |               |                           父表                           |
|      |          |        |               |                 emp.deptno = dept.deptno                 |
| 2）  |   dept   | deptno |     salno     |                           子表                           |
|      | salgrade | salno  |               |                           父表                           |
|      |          |        |               |               dept.salno = salgrade.salno                |
| 3）  |   emp    | empno  | deptno  salno |                           子表                           |
|      |   dept   | deptno |               |                           父表                           |
|      | salgrade | salno  |               |                           父表                           |
|      |          |        |               | emp.deptno = dept.deptno<br />emp.salno = salgrade.salno |

#### 非空约束：not null

#### 唯一约束：unique

#### 检查约束：check
---
title: '[Hive]-Hive 查询'
date: 2019-07-15 19:00:00
tags: Hive
categories: Hive
---

### 一.基本查询（Select…From）

#### 1.全表和特定列查询

##### (1).全表查询
```shell
hive (default)> select * from emp;
```
##### (2).选择特定列查询
```shell
hive (default)> select empno, ename from emp;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQwMGMzMGVhYTRjM2YxNTYucG5n?x-oss-process=image/format,png)

注意：

* SQL 语言大小写不敏感。
* SQL 可以写在一行或者多行
* 关键字不能被缩写也不能分行
* 各子句一般要分行写。
* 使用缩进提高语句的可读性。

#### 2. 列别名

##### (1).重命名一个列。

##### (2).便于计算。

##### (3).紧跟列名，也可以在列名和别名之间加入关键字‘AS’

##### (4).案例实操
###### (a)查询名称和部门

```shell
hive (default)> select ename AS name, deptno dn from emp;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTlhNmE5NjY3MThlZGQ4MjcucG5n?x-oss-process=image/format,png)

#### 3.算术运算符

|运算符|描述|
|:-:|:-:|
|A+B|A和B 相加|
|A-B|A减去B|
|A*B|A和B相乘|
|A/B|A除以B|
|A%B|A对B取余/模|
|A&B|A和B按位取与|
|A\|B|A和B按位取或|
|A\^B|A和B按位取异或|
|~A|A按位取反|


##### (1)案例实操

查询出所有员工的薪水后加1显示。
```shell
hive (default)> select sal +1 from emp;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWVhMTMxNDJlZDc5MzY3NzkucG5n?x-oss-process=image/format,png)

#### 4.常用函数

##### (1).求总行数（count）
```shell
hive (default)> select count(1) cnt from emp;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWYzYmEzOTQ3ZDMzNzgwM2MucG5n?x-oss-process=image/format,png)

##### (2).求工资的最大值（max）
```shell
hive (default)> select max(sal) max_sal from emp;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTM2MDYxYTZjNDA0ODc5NmYucG5n?x-oss-process=image/format,png)

##### (3).求工资的最小值（min）
```shell
hive (default)> select min(sal) min_sal from emp;
```
##### (4).求工资的总和（sum）
```shell
hive (default)> select sum(sal) sum_sal from emp; 
```
##### (5).求工资的平均值（avg）
```shell
hive (default)> select avg(sal) avg_sal from emp;
```
#### 5.Limit语句

典型的查询会返回多行数据。LIMIT子句用于限制返回的行数。
```shell
hive (default)> select * from emp limit 5;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LThjODQ0ZjFkZTk2N2I3ZjIucG5n?x-oss-process=image/format,png)

### 二.Where语句
#### 0.定义
##### (1).使用WHERE子句，将不满足条件的行过滤掉。

##### (2).WHERE子句紧随FROM子句。

##### (3).案例实操

查询出薪水大于1000的所有员工
```shell
hive (default)> select ename , sal from emp where sal > 1000;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY3NTU1NGI0YTU1YTk1NjgucG5n?x-oss-process=image/format,png)

#### 1.比较运算符（Between/In/ Is Null）

##### (1).下面表中描述了谓词操作符，这些操作符同样可以用于JOIN…ON和HAVING语句中。

|操作符|支持的数据类型|描述|
|:-:|:-:|:-:|
|A=B|基本数据类型|如果A等于B则返回TRUE，反之返回FALSE|
|A<=>B|基本数据类型|如果A和B都为NULL，则返回TRUE，其他的和等号（=）操作符的结果一致，如果任一为NULL则结果为NULL|
|A<>B, A!=B|基本数据类型|A或者B为NULL则返回NULL；如果A不等于B，则返回TRUE，反之返回FALSE|
|A<B|基本数据类型|A或者B为NULL，则返回NULL；如果A小于B，则返回TRUE，反之返回FALSE|
|A<=B|基本数据类型|A或者B为NULL，则返回NULL；如果A小于等于B，则返回TRUE，反之返回FALSE|
|A>B|基本数据类型|A或者B为NULL，则返回NULL；如果A大于B，则返回TRUE，反之返回FALSE|
|A>=B|基本数据类型|A或者B为NULL，则返回NULL；如果A大于等于B，则返回TRUE，反之返回FALSE|
|A [NOT] BETWEEN B AND C|基本数据类型|如果A，B或者C任一为NULL，则结果为NULL。如果A的值大于等于B而且小于或等于C，则结果为TRUE，反之为FALSE。如果使用NOT关键字则可达到相反的效果。|
|A IS NULL|所有数据类型|如果A等于NULL，则返回TRUE，反之返回FALSE|
|A IS NOT NULL|所有数据类型|如果A不等于NULL，则返回TRUE，反之返回FALSE|
|IN(数值1, 数值2)|所有数据类型|使用 IN运算显示列表中的值|
|A [NOT] LIKE B|STRING 类型|B是一个SQL下的简单正则表达式，如果A与其匹配的话，则返回TRUE；反之返回FALSE。B的表达式说明如下：‘x%’表示A必须以字母‘x’开头，‘%x’表示A必须以字母’x’结尾，而‘%x%’表示A包含有字母’x’,可以位于开头，结尾或者字符串中间。如果使用NOT关键字则可达到相反的效果。|
|A RLIKE B, A REGEXP B|STRING 类型|B是一个正则表达式，如果A与其匹配，则返回TRUE；反之返回FALSE。匹配使用的是JDK中的正则表达式接口实现的，因为正则也依据其中的规则。例如，正则表达式必须和整个字符串A相匹配，而不是只需与其字符串匹配。|

##### (2).案例实操

###### (a)查询出薪水等于5000的所有员工
```shell
hive (default)> select * from emp where sal =5000;
```
###### (b)查询工资在500到1000的员工信息，注意between ... and 是闭区间
```shell
hive (default)> select * from emp where sal between 500 and 1000;
```
###### (c )查询comm(奖金)为空的所有员工信息
```shell
hive (default)> select * from emp where comm is null;
```

###### (d)查询工资是1500和5000的员工信息
```shell
hive (default)> select * from emp where sal IN (1500, 5000);
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQ2NmNmYmIwMWNlNmVjYTkucG5n?x-oss-process=image/format,png)

#### 2.Like和RLike

##### (1).使用LIKE运算选择类似的值

##### (2).选择条件可以包含字符或数字:

* % 代表零个或多个字符(任意个字符)。

* _ 代表一个字符。


##### (3).RLIKE子句是Hive中这个功能的一个扩展，其可以通过Java的正则表达式这个更强大的语言来指定匹配条件。

##### (4).案例实操
###### (a)查找以2开头薪水的员工信息
```shell
hive (default)> select * from emp where sal LIKE '2%';
```
###### (b)查找第二个数值为2的薪水的员工信息
```shell
hive (default)> select * from emp where sal LIKE '_2%';
```
###### (c )查找薪水中含有2的员工信息
```shell
hive (default)> select * from emp where sal RLIKE '[2]';
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQ2NjQxZDNmNDFiYmYzYmYucG5n?x-oss-process=image/format,png)


#### 3.逻辑运算符（And/Or/Not）

|操作符|含义|
|:-:|:-:|
|AND|逻辑并|
|OR|逻辑或|
|NOT|逻辑否|

案例实操
##### (1)查询薪水大于1000，部门是30
```shell
hive (default)> select * from emp where sal>1000 and deptno=30;
```
##### (2)查询薪水大于1000，或者部门是30
```shell
hive (default)> select * from emp where sal>1000 or deptno=30;
```
##### (3)查询除了20部门和30部门以外的员工信息
```shell
hive (default)> select * from emp where deptno not IN(30, 20);
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWRmNGVhYmEzODU2MGU0YjYucG5n?x-oss-process=image/format,png)

### 三. 分组

#### 1.Group By语句

GROUP BY语句通常会和聚合函数一起使用，按照一个或者多个列队结果进行分组，然后对每个组执行聚合操作。

案例实操：

##### (1)计算emp表每个部门的平均工资
```shell
hive (default)> select t.deptno, avg(t.sal) avg_sal from emp t group by t.deptno;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWI3NWUxNDM1NTIxMTlhNGMucG5n?x-oss-process=image/format,png)

##### (2)计算emp每个部门中每个岗位的最高薪水
```shell
hive (default)> select t.deptno, t.job, max(t.sal) max_sal from emp t group by t.deptno, t.job;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWNjYzIzODY3Y2Y4ZmNiNjEucG5n?x-oss-process=image/format,png)

#### 2. Having语句

##### (1).having与where不同点

* where针对表中的列发挥作用，查询数据；having针对查询结果中的列发挥作用，筛选数据。
* where后面不能写分组函数，而having后面可以使用分组函数。

* having只用于group by分组统计语句。

##### (2).案例实操：

###### (a)求每个部门的平均薪水大于2000的部门

求每个部门的平均工资
```shell
hive (default)> select deptno, avg(sal) from emp group by deptno;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFmMWY0YmZlMWYxNDViMDgucG5n?x-oss-process=image/format,png)

###### (b)求每个部门的平均薪水大于2000的部门
```shell
hive (default)> select deptno, avg(sal) avg_sal from emp group by deptno having avg_sal > 2000;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWI1YWZiYWY5ZDk3MmQxY2YucG5n?x-oss-process=image/format,png)

### 四.Join语句

#### 1.等值Join

Hive支持通常的SQL JOIN语句，但是只支持等值连接，不支持非等值连接。

案例实操

##### (1)根据员工表和部门表中的部门编号相等，查询员工编号、员工名称和部门编号；
```shell
hive (default)> select e.empno, e.ename, d.deptno, d.dname from emp e join dept d on e.deptno = d.deptno;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWEwZjA4ZTRiMDQ3ZmRkMWUucG5n?x-oss-process=image/format,png)

同样与
```shell
hive (default)> select e.empno,e.ename,d.deptno,d.dname from emp e,dept d where e.deptno=d.deptno;
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTIxZDBiZDdmNDA5ZGNkMDMucG5n?x-oss-process=image/format,png)

#### 2. 表的别名

##### (1).好处
* 使用别名可以简化查询。
* 使用表名前缀可以提高执行效率。

##### (2).案例实操

合并员工表和部门表
```shell
hive (default)> select e.empno, e.ename, d.deptno from emp e join dept d on e.deptno = d.deptno;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTg2YTkzNGRmNTgwYmM1NDAucG5n?x-oss-process=image/format,png)

#### 3.内连接

内连接：只有进行连接的两个表中都存在与连接条件相匹配的数据才会被保留下来。
```shell
hive (default)> select e.empno, e.ename, d.deptno from emp e join dept d on e.deptno = d.deptno;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWEyODAwZjRhMzAyMDU3YjEucG5n?x-oss-process=image/format,png)

#### 4.左外连接

左外连接：JOIN操作符左边表中符合WHERE子句的所有记录将会被返回。
```shell
hive (default)> select e.empno, e.ename, d.deptno from emp e left join dept d on e.deptno = d.deptno;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQ1MjdhOTg3OWM0OWMzNDIucG5n?x-oss-process=image/format,png)

#### 5.右外连接

右外连接：JOIN操作符右边表中符合WHERE子句的所有记录将会被返回。
```shell
hive (default)> select e.empno, e.ename, d.deptno from emp e right join dept d on e.deptno = d.deptno;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTViZjc0NDRhYjg1OTViZGYucG5n?x-oss-process=image/format,png)

#### 6.满外连接

满外连接：将会返回所有表中符合WHERE语句条件的所有记录。如果任一表的指定字段没有符合条件的值的话，那么就使用NULL值替代。
```shell
hive (default)> select e.empno, e.ename, d.deptno from emp e full join dept d on e.deptno = d.deptno;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWM0NzY4NWMyM2I5YzgyNmYucG5n?x-oss-process=image/format,png)

#### 7.多表连接

注意：连接 n个表，至少需要n-1个连接条件。例如：连接三个表，至少需要两个连接条件。

##### (0)数据准备

##### (1)创建位置表
```shell
create table if not exists default.location(
loc int,
loc_name string
)
row format delimited fields terminated by '\t';
```
##### (2)导入数据
```shell
hive (default)> load data local inpath '/opt/module/datas/location.txt' into table default.location;
```
##### (3)多表连接查询distinct 
```shell
hive (default)>SELECT e.ename, d.deptno, l. loc_name
              FROM   emp e 
              JOIN   dept d
              ON     d.deptno = e.deptno 
              JOIN   location l
              ON     d.loc = l.loc;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQ5ZDQxZmVhZDM2NDBmYzMucG5n?x-oss-process=image/format,png)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWYwZTQyNDJiNWRiNjQyZDMucG5n?x-oss-process=image/format,png)

&nbsp;&nbsp;&nbsp;&nbsp;大多数情况下，Hive会对每对JOIN连接对象启动一个MapReduce任务。本例中会首先启动一个MapReduce job对表e和表d进行连接操作，然后会再启动一个MapReduce job将第一个MapReduce job的输出和表l;进行连接操作。

&nbsp;&nbsp;&nbsp;&nbsp;注意：为什么不是表d和表l先进行连接操作呢？这是因为Hive总是按照从左到右的顺序执行的。

#### 8.笛卡尔积

##### (1).笛卡尔集会在下面条件下产生:

* 省略连接条件
* 连接条件无效
* 所有表中的所有行互相连接

##### (2).案例实操
```shell
hive (default)> select empno, deptno from emp, dept;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQ4YmIwMGM3Njk5YmZkM2UucG5n?x-oss-process=image/format,png)

FAILED: SemanticException Column deptno Found in more than One Tables/Subqueries

#### 9.连接谓词中不支持or
```shell
hive (default)> select e.empno, e.ename, d.deptno from emp e join dept d on e.deptno = d.deptno or e.ename=d.ename;  
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWVmYWZiZWQyNjI1MGRjMTAucG5n?x-oss-process=image/format,png)

### 五.排序

#### 1.全局排序（Order By）

Order By：全局排序，一个MapReduce

##### (1).使用 ORDER BY 子句排序

* ASC（ascend）: 升序（默认）
* DESC（descend）: 降序

##### (2).ORDER BY 子句在SELECT语句的结尾。
##### (3).案例实操

###### (a)查询员工信息按工资升序排列
```shell
hive (default)> select * from emp order by sal;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTJlMjUyMDlmOGNkYWI4MzgucG5n?x-oss-process=image/format,png)

###### (b)查询员工信息按工资降序排列
```shell
hive (default)> select * from emp order by sal desc;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWQ4ODFjYTdjZGJkNGU3NzUucG5n?x-oss-process=image/format,png)

#### 2.按照别名排序

按照员工薪水的2倍排序
```shell
hive (default)> select ename, sal*2 twosal from emp order by twosal;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTgwMTJiNDdkZDVkNDkzNjUucG5n?x-oss-process=image/format,png)

#### 3.多个列排序

按照部门和工资升序排序
```shell
hive (default)> select ename, deptno, sal from emp order by deptno, sal ;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTY5YTgwYjRlZDUyNGU1ZGEucG5n?x-oss-process=image/format,png)

&nbsp;&nbsp;&nbsp;&nbsp;注：这个是先按部门号排序，部门号相同，按薪水升序排序

#### 4.每个MapReduce内部排序(Sort By)

&nbsp;&nbsp;&nbsp;&nbsp;Sort By：每个MapReduce内部进行排序，分区规则按照key的hash来运算，(区内排序)对全局结果集来说不是排序。

##### (1).设置reduce个数
```shell
hive (default)> set mapreduce.job.reduces=3;
```
##### (2).查看设置reduce个数
```shell
hive (default)> set mapreduce.job.reduces;
```
##### (3).根据部门编号降序查看员工信息
```shell
hive (default)> select * from emp sort by empno desc;
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFkNGU1YzRjMzhkZTQ5MDkucG5n?x-oss-process=image/format,png)
##### (4).将查询结果导入到文件中（按照部门编号降序排序）
```shell
hive (default)> insert overwrite local directory '/opt/module/datas/emp.txt' row format delimited fields terminated by '\t' select * from emp sort by deptno desc;
```

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTNkYzNlODlkMDkzZjVmZDgucG5n?x-oss-process=image/format,png)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWVkODYxNGI5MWZjZjZiZTMucG5n?x-oss-process=image/format,png)

#### 5.分区排序（Distribute By）

Distribute By：类似MR中partition，进行分区，结合sort by使用。

&nbsp;&nbsp;&nbsp;&nbsp;注意，Hive要求DISTRIBUTE BY语句要写在SORT BY语句之前。

&nbsp;&nbsp;&nbsp;&nbsp;对于distribute by进行测试，一定要分配多reduce进行处理，否则无法看到distribute by的效果。

案例实操：
##### (1)先按照部门编号分区，再按照员工编号降序排序。
```shell
hive (default)> set mapreduce.job.reduces=3;

hive (default)> insert overwrite local directory '/opt/module/datas/distribute-result' row format delimited fields terminated by '\t' select * from emp distribute by deptno sort by empno desc;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTk5YTA3OWM1N2MxMzQ0MzkucG5n?x-oss-process=image/format,png)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWNiMjM0NTM3MjNhZGQ2NTkucG5n?x-oss-process=image/format,png)


#### 6.Cluster By

&nbsp;&nbsp;&nbsp;&nbsp;当distribute by和sorts by字段相同时，可以使用cluster by方式。

&nbsp;&nbsp;&nbsp;&nbsp;cluster by除了具有distribute by的功能外还兼具sort by的功能。但是排序只能是倒序排序，不能指定排序规则为ASC或者DESC。

##### (1).以下两种写法等价
```shell
hive (default)> insert overwrite local directory '/opt/module/datas/cluster-result' row format delimited fields terminated by '\t' select * from emp cluster by deptno;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWE3YjlmYWUxNDQ3ZjBlN2IucG5n?x-oss-process=image/format,png)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTZlNjkwZDQwYTA3MGU3OTgucG5n?x-oss-process=image/format,png)

```shell
hive (default)> select * from emp distribute by deptno sort by deptno;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LThkZjAyNmFkNjEyZmUwZjcucG5n?x-oss-process=image/format,png)

&nbsp;&nbsp;&nbsp;&nbsp;注意：按照部门编号分区，不一定就是固定死的数值，可以是20号和30号部门分到一个分区里面去。

### 六.分桶及抽样查询

#### 1.分桶表数据存储
&nbsp;&nbsp;&nbsp;&nbsp;分区针对的是数据的存储路径；分桶针对的是数据文件。

&nbsp;&nbsp;&nbsp;&nbsp;分区提供一个隔离数据和优化查询的便利方式。不过，并非所有的数据集都可形成合理的分区，特别是之前所提到过的要确定合适的划分大小这个疑虑。

&nbsp;&nbsp;&nbsp;&nbsp;分桶是将数据集分解成更容易管理的若干部分的另一个技术。

##### (1).先创建分桶表，通过直接导入数据文件的方式

###### (a)数据准备

###### (b)创建分桶表
```shell
create table stu_buck(id int, name string)
clustered by(id) 
into 4 buckets
row format delimited fields terminated by '\t';
```
###### (c )查看表结构
```shell
hive (default)> desc formatted stu_buck;
```
Num Buckets:            4     
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBhNzExNTA1NDE5YjhhOTMucG5n?x-oss-process=image/format,png)

###### (d)导入数据到分桶表中
```shell
hive (default)> load data local inpath '/opt/module/datas/s/student.txt' into table stu_buck;
```
###### (e)查看创建的分桶表中是否分成4个桶

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWE0ZWRhNTQ1YTNlYmU4MWQucG5n?x-oss-process=image/format,png)

发现并没有分成4个桶。是什么原因呢？

##### (2).创建分桶表时，数据通过子查询的方式导入

###### (a)先建一个普通的stu表
```shell
create table stu(id int, name string)
row format delimited fields terminated by '\t';
```

###### (b)向普通的stu表中导入数据
```shell
load data local inpath '/opt/module/datas/s/student.txt' into table stu;
```
###### (c )清空stu_buck表中数据
```shell
truncate table stu_buck;

select * from stu_buck;
```
###### (d)导入数据到分桶表，通过子查询的方式
```shell
insert into table stu_buck
select id, name from stu;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTJkYjA5YzU5ZTE3YzQ1NTQucG5n?x-oss-process=image/format,png)

###### (e)发现还是只有一个分桶

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LThiNjMxMjliOGRhMjA2YmYucG5n?x-oss-process=image/format,png)

###### (g)需要设置一个属性
```shell
hive (default)> set hive.enforce.bucketing=true;

hive (default)> set mapreduce.job.reduces=-1;

hive (default)> truncate table stu_buck;

hive (default)> insert into table stu_buck
select id, name from stu;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTAwMTNkNDQ1ODlhY2QxNzcucG5n?x-oss-process=image/format,png)
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTQzMmY4NTUwMWYwZDg4NjQucG5n?x-oss-process=image/format,png)

###### (h)查询分桶的数据
```shell
hive (default)> select * from stu_buck;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWFjNzYwZmE5ZmExNTIyYWIucG5n?x-oss-process=image/format,png)


#### 2.分桶抽样查询

&nbsp;&nbsp;&nbsp;&nbsp;对于非常大的数据集，有时用户需要使用的是一个具有代表性的查询结果而不是全部结果。Hive可以通过对表进行抽样来满足这个需求。

查询表stu_buck中的数据。
```shell
hive (default)> select * from stu_buck tablesample(bucket 1 out of 4 on id);
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTJkM2M0ZGY4ZGJlMGVkYTYucG5n?x-oss-process=image/format,png)

注：tablesample是抽样语句，语法：TABLESAMPLE(BUCKET x OUT OF y) 。
```shell
hive (default)> select * from stu_buck tablesample(bucket 1 out of 3 on id);
```
不是桶数的倍数或者因子也可以，但是不推荐。

&nbsp;&nbsp;&nbsp;&nbsp;y必须是table总bucket数的倍数或者因子。hive根据y的大小，决定抽样的比例。例如，table总共分了4份，当y=2时，抽取(4/2=)2个bucket的数据，当y=8时，抽取(4/8=)1/2个bucket的数据。
```shell
hive (default)> select * from stu_buck tablesample(bucket 1 out of 2 on id);
```
![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LWU3NmFmZGM3NDhmMTlmZGYucG5n?x-oss-process=image/format,png)

&nbsp;&nbsp;&nbsp;&nbsp;x表示从哪个bucket开始抽取。例如，table总bucket数为4，tablesample(bucket 4 out of 4)，表示总共抽取（4/4=）1个bucket的数据，抽取第4个bucket的数据。
```shell
hive (default)> select * from stu_buck tablesample(bucket 1 out of 8 on id);
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTgyNjE4MTI0N2ViZTgyMjUucG5n?x-oss-process=image/format,png)

&nbsp;&nbsp;&nbsp;&nbsp;注意：x的值必须小于等于y的值，否则

FAILED: SemanticException [Error 10061]: Numerator should not be bigger than denominator in sample clause for table stu_buck

#### 3.数据块抽样

&nbsp;&nbsp;&nbsp;&nbsp;Hive提供了另外一种按照百分比进行抽样的方式，这种是基于行数的，按照输入路径下的数据块百分比进行的抽样。
```shell
hive (default)> select * from stu tablesample(0.1 percent) ;
```
![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy80MzkxNDA3LTBmNzQxZDY1ZTNiMmRiNzYucG5n?x-oss-process=image/format,png)

&nbsp;&nbsp;&nbsp;&nbsp;提示：这种抽样方式不一定适用于所有的文件格式。另外，这种抽样的最小抽样单元是一个HDFS数据块。因此，如果表的数据大小小于普通的块大小128M的话，那么将会返回所有行。


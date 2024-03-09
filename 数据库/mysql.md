# mysql

## 1.什么是数据库

>数据库（Database）是按照数据结构来组织、存储和管理数据的仓库。
>
>每个数据库都有一个或多个不同的 API 用于创建，访问，管理，搜索和复制所保存的数据。
>
>我们也可以将数据存储在文件中，但是在文件中读写数据速度相对较慢。
>
>所以，现在我们使用关系型数据库管理系统（RDBMS）来存储和管理大数据量。所谓的关系型数据库，是建立在关系模型基础上的数据库，借助于集合代数等数学概念和方法来处理数据库中的数据。
>
>RDBMS 即关系数据库管理系统(Relational Database Management System)的特点：
>
>- 1.数据以表格的形式出现
>- 2.每行为各种记录名称
>- 3.每列为记录名称所对应的数据域
>- 4.许多的行和列组成一张表单
>- 5.若干的表单组成database



## 2.RDBMS术语

> - **数据库:** 数据库是一些关联表的集合。
> - **数据表:** 表是数据的矩阵。在一个数据库中的表看起来像一个简单的电子表格。
> - **列:** 一列(数据元素) 包含了相同类型的数据, 例如邮政编码的数据。
> - **行：**一行（=元组，或记录）是一组相关的数据，例如一条用户订阅的数据。
> - **冗余**：存储两倍数据，冗余降低了性能，但提高了数据的安全性。
> - **主键**：主键是唯一的。一个数据表中只能包含一个主键。你可以使用主键来查询数据。
> - **外键：**外键用于关联两个表。
> - **复合键**：复合键（组合键）将多个列作为一个索引键，一般用于复合索引。
> - **索引：**使用索引可快速访问数据库表中的特定信息。索引是对数据库表中一列或多列的值进行排序的一种结构。类似于书籍的目录。
> - **参照完整性:** 参照的完整性要求关系中不允许引用不存在的实体。与实体完整性是关系模型必须满足的完整性约束条件，目的是保证数据的一致性。



## 3.基础

### 3.1 DDL

> DDL是数据定于语言的缩写，简单来说，就是对数据库内部的对象进行创建、删除、修改等操作的语言。它和DML语言的最大区别是DML只是对表内部数据操作，而不涉及表的定义，结构的修改，更不会涉及其他对象
>
> 数据库字段类型:
>
> 1.数值类型
>
> ![image-20221201103752264](C:\Users\hxh\Desktop\note\img\image-20221201103752264.png)
>
> 
>
> 2.字符串类型
>
> ![image-20221201104420699](C:\Users\hxh\Desktop\note\img\image-20221201104420699.png)
>
> 
>
> 3.日期时间类型
>
> ![image-20221201105701188](C:\Users\hxh\Desktop\note\img\image-20221201105701188.png)





#### 3.1.1 ddl操作数据库

```mysql
# 查询所有数据库
show databases;

# 创建数据库
create database if not exists test default charset utf8mb4;

# 删除数据库
drop database if exists test;

# 使用数据库
use test；

# 查询当前所在数据库
select database();
```

#### 3.1.2 ddl操作表

```mysql
# 查询当前数据库所有表
show tables;

# 查询表结构
desc student

# 查询指定表的建表语句
show create table student;

# 创建表
employee | CREATE TABLE `employee` (
  `id` int(11) NOT NULL,
  `worknum` varchar(10)NOT NULL,
  `name` varchar(10) NOT NULL,
  `gender` char(1) DEFAULT NULL,
  `age` tinyint(3) unsigned DEFAULT NULL,
  `id_card` char(18) DEFAULT NULL,
  `entry_date` date DEFAULT NULL,
   PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 

######################################################

# 修改表
# 为employee表增加新字段
alter table employee add nickname varchar(20) comment '昵称';

# 修改字段
alter table employee change nickname username varchar(30) comment '用户名';

# 删除字段
alter table employee drop username;

# 修改表名
alter table employee rename to emp;

######################################################

# 删除表
drop table if exists student;

# 删除表, 并重新创建表 目的删除表内所有数据
truncate table emp;
```



### 3.2 DML

#### 3.2.1 插入数据

> + 插入数据时, 指定的字段顺序与值顺序一致
> + 字符串与日期类型的数据应该包括在引号内
> + 插入的数据大小,应该在字段的规定范围内

```mysql
# 给指定字段赋值
# insert into 表名(字段1,字段2) values(值1,值2);
insert into student(id,name)values (1, "linux");

# 给所有字段赋值
# inert into 表名 values(值1,值2);
insert into student values (2, "java");

# 批量复制
# insert into 表名(字段1,字段2) values(值1,值2),(值1,值2),(值1,值2);
insert into student(id,name)values (3,"python"),(4,"c++");

# inert into 表名 values(值1,值2),(值1,值2),(值1,值2);
insert into student values (5,"go"),(6,"c#");
```



#### 3.2.2 更新、删除数据

```mysql
# 更新数据
# update 表名 set 字段1=值1, 字段2=值2 where=条件 不给条件 默认修改整张表
update student set id=7,name="php" where id=6;

# 删除数据
# delete from 表明 where=条件 不给条件 默认删除整表
delete from student where id=7;
```



### 3.3 DQL

#### 3.3.1 基础查询

```mysql
/*
select
	字段列表
where
	条件列表
group by
	分组字段列表
having
	分组后条件列表
order by
	排序字段列表
limit
	分页参数
*/



# 查询指定字段数据,并起别名
# as可以省略
select username as "姓名",real_name "真实姓名" from user

# 查询返回所有字段数据
# select id,username ... from user # 不建议使用select * from user; 影响查询效率

# 查询所有地址,要求去除重复记录
select distinct address from user;
```



#### 3.3.2 条件查询

![image-20221201122408850](C:\Users\hxh\Desktop\note\img\image-20221201122408850.png)

---

![image-20221201122441468](C:\Users\hxh\Desktop\note\img\image-20221201122441468.png)

```mysql
# 查询年龄大于18的用户
select * from user where age>18;

# 查询年龄在18到30的用户
select * from user where age>=18 and age<=30;
select * from user where age between 18 and 30;

# 查询身份证信息是空的用户
select * from user where id_card is null;
# 查询身份证信息不是空的用户
select * from user where id_card is not null;

# 查询性别是女并且年龄大于18
select * from user  where gender="女" and age>18;

# 查询年龄是女或者年龄是18或者地址不为空的用户
select * from user where gender="女" or age=18 or address is not null;

# 查询年龄是18,20,30的用户
select * from user where age in(18,20,30);

# 查询姓名是两个字的用户
select * from user where username like '__';

# 查询姓赵的用户
select * from user where real_name like '赵%';

# 查询姓赵,且姓名只有两个字的用户
select * from user where real_name like '赵_';
```



#### 3.3.3 聚合函数

> 将一列数据作为一个整体, 进行纵向计算，null字段不参数计算
>
> 常见的聚合函数
>
> | 函数  |   功能   |
> | :---: | :------: |
> | count | 统计数量 |
> |  max  |  最大值  |
> |  min  |  最小值  |
> |  avg  |  平均值  |
> |  sum  |   求和   |

```mysql
# select 聚合函数(字段列表) from 表名

# 统计用户数量
select count(id) from user;

# 统计平均年龄
select avg(age) from user;

# 统计性别是女的的年龄之和
select sum(age) from user where gender="女";
```



#### 3.3.4 分组查询

>分组之后，查询的字段一般都是聚合函数,分组字段  查询其他字段是没有意义的
>
>where 与having的区别:
>
>+ 执行时机的不同：where是分组之前过滤数据,不满足条件的数据不参与分组, having则是对分组后的结果过滤
>+ 判断条件的不同： where不能对聚合函数判断, 而having可以

```mysql
# select 字段列表 from 表名 [where 条件] group by 分组字段名 [having 分组后过滤条件];

# 按性别分组 统计男性，女性用户的数量
select gender "性别", count(*) user from user group by gender;

# 按性别分组 统计男性，女性用户平均年龄
select gender "性别", avg(age) user from user group by gender;

# 查询年龄小于60,按地址分组, 且地址数量等于2的地址信息
select adderss ,count(*) as num from user where age<60 group by address having num=2 ;
```



#### 3.3.5 排序查询

> 排序方式:
>
> ASC 升序  DESC降序
>
> 多字段排序，当第一个字段相同才会根据第二个字段排序,以此类推

```mysql
# select 字段列表 from 表名 order by 字段1 排序方式,字段2 排序方式
select * from user order by age asc,id desc;
```



#### 3.3.6分页查询

> 起始索引 (查询页码-1)*每页显示数量
>
> 分页查询是数据库的方言,不同的数据库有不同的实现 mysql是limit
>
> 查询第一页数据 起始索引可以省略 limit 10

```mysql
# select 字段列表 from 表名 limit 起始索引,查询的记录数

# 查询第一页数据 select * from user limit (1-1)*10,10;
select * from user limit 0,10;

# 查询第二页数据 select * from user limit (2-1)*10,10;
select * from user limit 10,10;
```



#### 3.3.7执行顺序

> ![image-20221201151407645](C:\Users\hxh\Desktop\note\img\image-20221201151407645.png)



### 3.4 DCL

#### 3.4.1用户管理

```mysql
# 查询系统用户
use mysql;

select * from user;

# 创建用户
# ‘用户名’@'主机ip' 本地=localhost  任意主机=% 
create user "hxh"@"localhost" identified by "123456";

# 创建用户,允许任意主机登录
create user "hxh"@"%" identified by "123456";

# 修改用户密码
alter user 'hxh@localhost'@'%' identified with mysql_native_password by 'root'

# 删除用户
drop user 'hxh'@'localhost';
```



#### 3.4.2权限控制

![image-20221201155022401](C:\Users\hxh\Desktop\note\img\image-20221201155022401.png)

```mysql
# 查询用户权限
show grants for "hxh"@"localhost";

# 授予用户 test 数据库中 user 表的crud权限
# grant 权限列表 on 数据库名.表名 to 用户名@主机名
grant select,update,delete,create on test.user to "hxh"@"localhost";

# 授予用户全部数据库的全部权限
grant all on *.* to "hxh"@"localhost";

# 撤销权限
# revoke 权限列表 on 数据库名.表名 from 用户名@主机名
revoke delete on test.user from "hxh"@"localhost";
```



### 3.5 函数

#### 3.5.1 字符串函数

![image-20221201161106940](C:\Users\hxh\Desktop\note\img\image-20221201161106940.png)

```mysql
select concat("hello","world");
select lower("HELLO");
select upper("hello");
select lpad("01",5,'-');
select rpad("01",5,'+');
select trim(" h x h ");
select substring("12345",1,2);

update set num=lpad(num,5,"0");
```



#### 3.5.2 数值函数

![image-20221201162244551](C:\Users\hxh\Desktop\note\img\image-20221201162244551.png)

```mysql
# 平均年龄四舍五入
select gender, round(avg(age),0) from test.user group by gender
```



#### 3.5.3 日期函数

![image-20221201162750468](C:\Users\hxh\Desktop\note\img\image-20221201162750468.png)

```mysql
select curdate();
select curtime();
select now();
select year(now());
select month(now());
select day(now());
select date_add(now(),interval 70 month );
select datediff(now(),date_add(now(),interval 70 day ));

select username,datediff(now(),create_time) as d  from user order by d  desc 
```



#### 3.5.3 流程控制函数

![image-20221201163952135](C:\Users\hxh\Desktop\note\img\image-20221201163952135.png)

```mysql
select if(1 = 1, "ok", "false");
select ifnull(null, "fail");

# 如果性别是男返回 呵呵,女返回 嘻嘻,其他情况返回 恶心
select username,
       case gender when '男' then "呵呵" when "女" then "嘻嘻" else "恶心!" end
from test.user;
```



### 3.6 约束

> 约束是作用于表字段上的规则，用来限制存储在表中的数据
>
> 其目的是为了保证数据的正确有效且完整
>
> ![image-20221201165237345](C:\Users\hxh\Desktop\note\img\image-20221201165237345.png)

```mysql
create table employee(
    id int primary key auto_increment comment '主键',
    name varchar(10) not null unique comment '姓名',
    age int check ( 18<age && age<30 ) comment '年龄',
    status char(1) default '1',
    gender char(1)
)comment '员工表';

alter table emp add constraint fk_emp_dept_id foreign key(dept_id) references dept(id) on update set null on delete set null;
```



### 3.7 多表查询

#### 3.7.1 内连接

> 内连接查询两张表的交集

```mysql
# 隐式内连接
select * from tb_user u,tb_user_edu ue where u.id=ue.userid;

# 显示内连接
select * from tb_user u inner join tb_user_edu ue on u.id = ue.userid;
```



#### 3.7.2 外连接

>左外连接 查询左边的所有数据 以及 两张表交集部分数据
>
>右外连接 查询右边的所有数据 以及 两张表交集部分数据

```mysql
select s.id,s.name,s.no,c.name from student s left join course c on s.id=c.id;

select s.id,s.name,s.no,c.name from student s right join course c on s.id=c.id;
```



#### 3.7.3自连接

> 查询当前表与自身连接的查询, 自连接必须使用别名

```mysql
# 查询员工和员工领导的姓名
select s.name,sp.name from student s join student sp where s.ofid=sp.id;
select s.name,sp.name from student s , student sp where s.ofid=sp.id;

# 查询员工和员工领导的姓名 即使领导为空也要查询出来
select s.name,sp.name from student s left join student sp on s.ofid=sp.id;
```

![image-20221201192455320](C:\Users\hxh\Desktop\note\img\image-20221201192455320.png)



#### 3.7.4 联合查询

> 联合查询就是把多次查询的结果合并起来 形成一个新的结果集
>
> 联合查询的多张表列数必须保持一致,字段类型也要保持一致

```mysql
select * from user where age>30
union all  # 去掉关键字 all 可以实现去重效果
select * from user where username like '张%';
```



#### 3.7.5 子查询

> sql语句中嵌套select语句称为嵌套查询，也称为子查询

+ 标量子查询 (子查询的结果为单个值）

  ```mysql
  # 操作符 > < = !=
  select * from course where id =(select no from student where name = 'hxh');
  ```

+ 列子查询（子查询的结果是一列）

  > ![image-20221201200046211](C:\Users\hxh\Desktop\note\img\image-20221201200046211.png)

  ```mysql
  select * from course where id in (select no from student where id > 2);
  ```

+ 行子查询 （子查询的结果是一行）

  ```mysql
  # 操作符 = 、<> 、in 、not in 
  select * from user where (age,gender) =(select age,gender from user where id=1);
  ```

+ 表子查询 (子查询的结果是多行多列)

  ```mysql
  # gender和age 可以匹配子查询多行数据的任意一行
  select * from user where (gender,age) in ( select gender,age from user where id>3);
  ```

  



### 3.8 事务

>MySQL 事务主要用于处理操作量大，复杂度高的数据。比如说，在人员管理系统中，你删除一个人员，你既需要删除人员的基本资料，也要删除和该人员相关的信息，如信箱，文章等等，这样，这些数据库操作语句就构成一个事务！
>
>- 在 MySQL 中只有使用了 Innodb 数据库引擎的数据库或表才支持事务。
>- 事务处理可以用来维护数据库的完整性，保证成批的 SQL 语句要么全部执行，要么全部不执行。
>- 事务用来管理 insert,update,delete 语句
>
>一般来说，事务是必须满足4个条件（ACID）：：原子性（**A**tomicity，或称不可分割性）、一致性（**C**onsistency）、隔离性（**I**solation，又称独立性）、持久性（**D**urability）。
>
>- **原子性：**一个事务（transaction）中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。
>- **一致性：**在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作。
>- **隔离性：**数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括读未提交（Read uncommitted）、读提交（read committed）、可重复读（repeatable read）和串行化（Serializable）。
>- **持久性：**事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。



#### 3.8.1事务的操作

```mysql
# 查看事务的提交方式
select @@autocommit;

# 禁止事务自动提交
set @@autocommit=0;

# 手动开启事务
start transaction;

# 提交事务
commit;

# 回滚事务
rollback;
```





#### 3.8.2并发事务问题

![image-20221201212336756](C:\Users\hxh\Desktop\note\img\image-20221201212336756.png)

![image-20221201221422920](C:\Users\hxh\Desktop\note\img\image-20221201221422920.png)



```mysql
# 查看事务的隔离级别
select @@transaction_isolation;

# 设置事务的隔离级别
# set [session|global] transaction isolation level [read uncommitted|read committed...]
set session transaction isolation level serializable ;
```


log: MySQL 数据库 2023年9月4日-2023年9月14日 10天

# 基础篇

## 通用语法及分类

- DDL: 数据定义语言，用来定义数据库对象（数据库、表、字段）
- DML: 数据操作语言，用来对数据库表中的数据进行增删改
- DQL: 数据查询语言，用来查询数据库中表的记录
- DCL: 数据控制语言，用来创建数据库用户、控制数据库的控制权限

### DDL（数据定义语言）

数据定义语言

#### 数据库操作

查询所有数据库：
`SHOW DATABASES;`
查询当前数据库：
`SELECT DATABASE();`
创建数据库：
`CREATE DATABASE [ IF NOT EXISTS ] 数据库名 [ DEFAULT CHARSET 字符集] [COLLATE 排序规则 ];`
删除数据库：
`DROP DATABASE [ IF EXISTS ] 数据库名;`
使用数据库：
`USE 数据库名;`

##### 注意事项

- UTF8字符集长度为3字节，有些符号占4字节，所以推荐用utf8mb4字符集

#### 表操作

查询当前数据库所有表：
`SHOW TABLES;`
查询表结构：
`DESC 表名;`
查询指定表的建表语句：
`SHOW CREATE TABLE 表名;`

创建表：
```mysql
CREATE TABLE 表名(
	字段1 字段1类型 [COMMENT 字段1注释],
	字段2 字段2类型 [COMMENT 字段2注释],
	字段3 字段3类型 [COMMENT 字段3注释],
	...
	字段n 字段n类型 [COMMENT 字段n注释]
)[ COMMENT 表注释 ];
```
**最后一个字段后面没有逗号**

添加字段：
`ALTER TABLE 表名 ADD 字段名 类型(长度) [COMMENT 注释] [约束];`
例：`ALTER TABLE emp ADD nickname varchar(20) COMMENT '昵称';`

修改数据类型：
`ALTER TABLE 表名 MODIFY 字段名 新数据类型(长度);`
修改字段名和字段类型：
`ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型(长度) [COMMENT 注释] [约束];`
例：将emp表的nickname字段修改为username，类型为varchar(30)
`ALTER TABLE emp CHANGE nickname username varchar(30) COMMENT '昵称';`

删除字段：
`ALTER TABLE 表名 DROP 字段名;`

修改表名：
`ALTER TABLE 表名 RENAME TO 新表名`

删除表：
`DROP TABLE [IF EXISTS] 表名;`
删除表，并重新创建该表：
`TRUNCATE TABLE 表名;`

### DML（数据操作语言）

#### 添加数据

指定字段：
`INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...);`
全部字段：
`INSERT INTO 表名 VALUES (值1, 值2, ...);`

批量添加数据：
`INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...);`
`INSERT INTO 表名 VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...);`

##### 注意事项

- 字符串和日期类型数据应该包含在引号中
- 插入的数据大小应该在字段的规定范围内

#### 更新和删除数据

修改数据：
`UPDATE 表名 SET 字段名1 = 值1, 字段名2 = 值2, ... [ WHERE 条件 ];`
例：
`UPDATE emp SET name = 'Jack' WHERE id = 1;`

删除数据：
`DELETE FROM 表名 [ WHERE 条件 ];`

### DQL（数据查询语言）

语法：
```mysql
SELECT
	字段列表
FROM
	表名字段
WHERE
	条件列表
GROUP BY
	分组字段列表
HAVING
	分组后的条件列表
ORDER BY
	排序字段列表
LIMIT
	分页参数
```

#### 基础查询

查询多个字段：
`SELECT 字段1, 字段2, 字段3, ... FROM 表名;`
`SELECT * FROM 表名;`

设置别名：
`SELECT 字段1 [ AS 别名1 ], 字段2 [ AS 别名2 ], 字段3 [ AS 别名3 ], ... FROM 表名;`
`SELECT 字段1 [ 别名1 ], 字段2 [ 别名2 ], 字段3 [ 别名3 ], ... FROM 表名;`

去除重复记录：
`SELECT DISTINCT 字段列表 FROM 表名;`

转义：
`SELECT * FROM 表名 WHERE name LIKE '/_张三' ESCAPE '/'`
/ 之后的\_不作为通配符

#### 条件查询

语法：
`SELECT 字段列表 FROM 表名 WHERE 条件列表;`

条件：

| 比较运算符          | 功能                                        |
| ------------------- | ------------------------------------------- |
| >                   | 大于                                        |
| >=                  | 大于等于                                    |
| <                   | 小于                                        |
| <=                  | 小于等于                                    |
| =                   | 等于                                        |
| <> 或 !=            | 不等于                                      |
| BETWEEN ... AND ... | 在某个范围内（含最小、最大值）              |
| IN(...)             | 在in之后的列表中的值，多选一                |
| LIKE 占位符         | 模糊匹配（\_匹配单个字符，%匹配任意个字符） |
| IS NULL             | 是NULL                                      |

| 逻辑运算符         | 功能                         |
| ------------------ | ---------------------------- |
| AND 或 &&          | 并且（多个条件同时成立）     |
| OR 或 &#124;&#124; | 或者（多个条件任意一个成立） |
| NOT 或 !           | 非，不是                     |

例子：
```mysql
-- 年龄等于30
select * from employee where age = 30;
-- 年龄小于30
select * from employee where age < 30;
-- 小于等于
select * from employee where age <= 30;
-- 没有身份证
select * from employee where idcard is null or idcard = '';
-- 有身份证
select * from employee where idcard;
select * from employee where idcard is not null;
-- 不等于
select * from employee where age != 30;
-- 年龄在20到30之间
select * from employee where age between 20 and 30;
select * from employee where age >= 20 and age <= 30;
-- 下面语句不报错，但查不到任何信息
select * from employee where age between 30 and 20;
-- 性别为女且年龄小于30
select * from employee where age < 30 and gender = '女';
-- 年龄等于25或30或35
select * from employee where age = 25 or age = 30 or age = 35;
select * from employee where age in (25, 30, 35);
-- 姓名为两个字
select * from employee where name like '__';
-- 身份证最后为X
select * from employee where idcard like '%X';
```

#### 聚合查询（聚合函数）

常见聚合函数：

| 函数  | 功能     |
| ----- | -------- |
| count | 统计数量 |
| max   | 最大值   |
| min   | 最小值   |
| avg   | 平均值   |
| sum   | 求和     |

语法：
`SELECT 聚合函数(字段列表) FROM 表名;`
例：
`SELECT count(id) from employee where workaddress = "广东省";`

#### 分组查询

语法：
`SELECT 字段列表 FROM 表名 [ WHERE 条件 ] GROUP BY 分组字段名 [ HAVING 分组后的过滤条件 ];`

where 和 having 的区别：

- 执行时机不同：where是分组之前进行过滤，不满足where条件不参与分组；having是分组后对结果进行过滤。
- 判断条件不同：where不能对聚合函数进行判断，而having可以。

例子：

```mysql
-- 根据性别分组，统计男性和女性数量（只显示分组数量，不显示哪个是男哪个是女）
select count(*) from employee group by gender;
-- 根据性别分组，统计男性和女性数量
select gender, count(*) from employee group by gender;
-- 根据性别分组，统计男性和女性的平均年龄
select gender, avg(age) from employee group by gender;
-- 年龄小于45，并根据工作地址分组
select workaddress, count(*) from employee where age < 45 group by workaddress;
-- 年龄小于45，并根据工作地址分组，获取员工数量大于等于3的工作地址
select workaddress, count(*) address_count from employee where age < 45 group by workaddress having address_count >= 3;
```

##### 注意事项

- 执行顺序：where > 聚合函数 > having
- 分组之后，查询的字段一般为聚合函数和分组字段，查询其他字段无任何意义

#### 排序查询

语法：
`SELECT 字段列表 FROM 表名 ORDER BY 字段1 排序方式1, 字段2 排序方式2;`

排序方式：

- ASC: 升序（默认）
- DESC: 降序

例子：

```mysql
-- 根据年龄升序排序
SELECT * FROM employee ORDER BY age ASC;
SELECT * FROM employee ORDER BY age;
-- 两字段排序，根据年龄升序排序，入职时间降序排序
SELECT * FROM employee ORDER BY age ASC, entrydate DESC;
```

##### 注意事项

如果是多字段排序，当第一个字段值相同时，才会根据第二个字段进行排序

#### 分页查询

语法：
`SELECT 字段列表 FROM 表名 LIMIT 起始索引, 查询记录数;`

例子：

```mysql
-- 查询第一页数据，展示10条
SELECT * FROM employee LIMIT 0, 10;
-- 查询第二页
SELECT * FROM employee LIMIT 10, 10;
```

##### 注意事项

- 起始索引从0开始，起始索引 = （查询页码 - 1） * 每页显示记录数
- 分页查询是数据库的方言，不同数据库有不同实现，MySQL是LIMIT
- 如果查询的是第一页数据，起始索引可以省略，直接简写 LIMIT 10

#### DQL执行顺序

FROM -> WHERE -> GROUP BY -> SELECT -> ORDER BY -> LIMIT

### DCL

#### 管理用户

查询用户：

```mysql
USE mysql;
SELECT * FROM user;
```

创建用户:
`CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';`

修改用户密码：
`ALTER USER '用户名'@'主机名' IDENTIFIED WITH mysql_native_password BY '新密码';`

删除用户：
`DROP USER '用户名'@'主机名';`

例子：

```mysql
-- 创建用户test，只能在当前主机localhost访问
create user 'test'@'localhost' identified by '123456';
-- 创建用户test，能在任意主机访问
create user 'test'@'%' identified by '123456';
create user 'test' identified by '123456';
-- 修改密码
alter user 'test'@'localhost' identified with mysql_native_password by '1234';
-- 删除用户
drop user 'test'@'localhost';
```

##### 注意事项

- 主机名可以使用 % 通配

#### 权限控制

常用权限：

| 权限                | 说明               |
| ------------------- | ------------------ |
| ALL, ALL PRIVILEGES | 所有权限           |
| SELECT              | 查询数据           |
| INSERT              | 插入数据           |
| UPDATE              | 修改数据           |
| DELETE              | 删除数据           |
| ALTER               | 修改表             |
| DROP                | 删除数据库/表/视图 |
| CREATE              | 创建数据库/表      |

更多权限请看[权限一览表](#权限一览表 "权限一览表")

查询权限：
`SHOW GRANTS FOR '用户名'@'主机名';`

授予权限：
`GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';`

撤销权限：
`REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';`

##### 注意事项

- 多个权限用逗号分隔
- 授权时，数据库名和表名可以用 * 进行通配，代表所有

## 函数

- 字符串函数
- 数值函数
- 日期函数
- 流程函数

### 字符串函数

常用函数：

| 函数  | 功能  |
| ------------ | ------------ |
| CONCAT(s1, s2, ..., sn)  | 字符串拼接，将s1, s2, ..., sn拼接成一个字符串  |
| LOWER(str)  | 将字符串全部转为小写  |
| UPPER(str)  | 将字符串全部转为大写  |
| LPAD(str, n, pad)  | 左填充，用字符串pad对str的左边进行填充，达到n个字符串长度  |
| RPAD(str, n, pad)  | 右填充，用字符串pad对str的右边进行填充，达到n个字符串长度  |
| TRIM(str)  | 去掉字符串头部和尾部的空格  |
| SUBSTRING(str, start, len)  | 返回从字符串str从start位置起的len个长度的字符串  |
| REPLACE(column, source, replace)  | 替换字符串  |

使用示例：

```mysql
-- 拼接
SELECT CONCAT('Hello', 'World');
-- 小写
SELECT LOWER('Hello');
-- 大写
SELECT UPPER('Hello');
-- 左填充
SELECT LPAD('01', 5, '-');
-- 右填充
SELECT RPAD('01', 5, '-');
-- 去除空格
SELECT TRIM(' Hello World ');
-- 切片（起始索引为1）
SELECT SUBSTRING('Hello World', 1, 5);
```

### 数值函数

常见函数：

| 函数  | 功能  |
| ------------ | ------------ |
| CEIL(x)  | 向上取整  |
| FLOOR(x)  | 向下取整  |
| MOD(x, y)  | 返回x/y的模  |
| RAND() | 返回0~1内的随机数 |
| ROUND(x, y) | 求参数x的四舍五入值，保留y位小数 |

### 日期函数

常用函数：

| 函数  | 功能  |
| ------------ | ------------ |
| CURDATE()  | 返回当前日期  |
| CURTIME()  | 返回当前时间  |
| NOW()  | 返回当前日期和时间  |
| YEAR(date)  | 获取指定date的年份  |
| MONTH(date)  | 获取指定date的月份  |
| DAY(date)  | 获取指定date的日期  |
| DATE_ADD(date, INTERVAL expr type)  | 返回一个日期/时间值加上一个时间间隔expr后的时间值  |
| DATEDIFF(date1, date2)  | 返回起始时间date1和结束时间date2之间的天数  |

例子：

```mysql
-- DATE_ADD
SELECT DATE_ADD(NOW(), INTERVAL 70 YEAR);
```

### 流程函数

常用函数：

| 函数  | 功能  |
| ------------ | ------------ |
| IF(value, t, f)  | 如果value为true，则返回t，否则返回f  |
| IFNULL(value1, value2)  | 如果value1不为空，返回value1，否则返回value2  |
| CASE WHEN [ val1 ] THEN [ res1 ] ... ELSE [ default ] END  | 如果val1为true，返回res1，... 否则返回default默认值  |
| CASE [ expr ] WHEN [ val1 ] THEN [ res1 ] ... ELSE [ default ] END  | 如果expr的值等于val1，返回res1，... 否则返回default默认值  |

例子：

```mysql
select
	name,
	(case when age > 30 then '中年' else '青年' end)
from employee;
select
	name,
	(case workaddress when '北京市' then '一线城市' when '上海市' then '一线城市' else '二线城市' end) as '工作地址'
from employee;
```

## 约束

分类：

| 约束  | 描述  | 关键字  |
| ------------ | ------------ | ------------ |
| 非空约束  | 限制该字段的数据不能为null  | NOT NULL  |
| 唯一约束  | 保证该字段的所有数据都是唯一、不重复的  | UNIQUE  |
| 主键约束  | 主键是一行数据的唯一标识，要求非空且唯一  | PRIMARY KEY  |
| 默认约束  | 保存数据时，如果未指定该字段的值，则采用默认值  | DEFAULT  |
| 检查约束（8.0.1版本后）  | 保证字段值满足某一个条件  | CHECK  |
| 外键约束  | 用来让两张图的数据之间建立连接，保证数据的一致性和完整性  | FOREIGN KEY  |

约束是作用于表中字段上的，可以再创建表/修改表的时候添加约束。

### 常用约束

| 约束条件  | 关键字  |
| ------------ | ------------ |
| 主键  | PRIMARY KEY  |
| 自动增长  | AUTO_INCREMENT  |
| 不为空  | NOT NULL  |
| 唯一  | UNIQUE  |
| 逻辑条件  | CHECK  |
| 默认值  | DEFAULT  |

例子：

```mysql
create table user(
	id int primary key auto_increment,
	name varchar(10) not null unique,
	age int check(age > 0 and age < 120),
	status char(1) default '1',
	gender char(1)
);
```

### 外键约束

添加外键：

```mysql
CREATE TABLE 表名(
	字段名 字段类型,
	...
	[CONSTRAINT] [外键名称] FOREIGN KEY(外键字段名) REFERENCES 主表(主表列名)
);
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名) REFERENCES 主表(主表列名);

-- 例子
alter table emp add constraint fk_emp_dept_id foreign key(dept_id) references dept(id);
```

删除外键：
`ALTER TABLE 表名 DROP FOREIGN KEY 外键名;`

#### 删除/更新行为

| 行为  | 说明  |
| ------------ | ------------ |
| NO ACTION  | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新（与RESTRICT一致）  |
| RESTRICT  | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新（与NO ACTION一致）  |
| CASCADE  | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则也删除/更新外键在子表中的记录  |
| SET NULL  | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中该外键值为null（要求该外键允许为null）  |
| SET DEFAULT  | 父表有变更时，子表将外键设为一个默认值（Innodb不支持）  |

更改删除/更新行为：
`ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段) REFERENCES 主表名(主表字段名) ON UPDATE 行为 ON DELETE 行为;`

## 多表查询

### 多表关系

- 一对多（多对一）
- 多对多
- 一对一

#### 一对多

案例：部门与员工
关系：一个部门对应多个员工，一个员工对应一个部门
实现：在多的一方建立外键，指向一的一方的主键

#### 多对多

案例：学生与课程
关系：一个学生可以选多门课程，一门课程也可以供多个学生选修
实现：建立第三张中间表，中间表至少包含两个外键，分别关联两方主键

#### 一对一

案例：用户与用户详情
关系：一对一关系，多用于单表拆分，将一张表的基础字段放在一张表中，其他详情字段放在另一张表中，以提升操作效率
实现：在任意一方加入外键，关联另外一方的主键，并且设置外键为唯一的（UNIQUE）

### 查询

合并查询（笛卡尔积，会展示所有组合结果）：
`select * from employee, dept;`

> 笛卡尔积：两个集合A集合和B集合的所有组合情况（在多表查询时，需要消除无效的笛卡尔积）

消除无效笛卡尔积：
`select * from employee, dept where employee.dept = dept.id;`

### 内连接查询

内连接查询的是两张表交集的部分

隐式内连接：
`SELECT 字段列表 FROM 表1, 表2 WHERE 条件 ...;`

显式内连接：
`SELECT 字段列表 FROM 表1 [ INNER ] JOIN 表2 ON 连接条件 ...;`

显式性能比隐式高

例子：

```mysql
-- 查询员工姓名，及关联的部门的名称
-- 隐式
select e.name, d.name from employee as e, dept as d where e.dept = d.id;
-- 显式
select e.name, d.name from employee as e inner join dept as d on e.dept = d.id;
```

### 外连接查询

左外连接：
查询左表所有数据，以及两张表交集部分数据
`SELECT 字段列表 FROM 表1 LEFT [ OUTER ] JOIN 表2 ON 条件 ...;`
相当于查询表1的所有数据，包含表1和表2交集部分数据

右外连接：
查询右表所有数据，以及两张表交集部分数据
`SELECT 字段列表 FROM 表1 RIGHT [ OUTER ] JOIN 表2 ON 条件 ...;`

例子：

```mysql
-- 左
select e.*, d.name from employee as e left outer join dept as d on e.dept = d.id;
select d.name, e.* from dept d left outer join emp e on e.dept = d.id;  -- 这条语句与下面的语句效果一样
-- 右
select d.name, e.* from employee as e right outer join dept as d on e.dept = d.id;
```

左连接可以查询到没有dept的employee，右连接可以查询到没有employee的dept

### 自连接查询

当前表与自身的连接查询，自连接必须使用表别名

语法：
`SELECT 字段列表 FROM 表A 别名A JOIN 表A 别名B ON 条件 ...;`

自连接查询，可以是内连接查询，也可以是外连接查询

例子：

```mysql
-- 查询员工及其所属领导的名字
select a.name, b.name from employee a, employee b where a.manager = b.id;
-- 没有领导的也查询出来
select a.name, b.name from employee a left join employee b on a.manager = b.id;
```

领导也在表里

### 联合查询 union, union all

把多次查询的结果合并，形成一个新的查询集

语法：

```mysql
SELECT 字段列表 FROM 表A ...
UNION [ALL]
SELECT 字段列表 FROM 表B ...
```

#### 注意事项

- UNION ALL 会有重复结果，UNION 不会
- 联合查询比使用or效率高，不会使索引失效

### 子查询

SQL语句中嵌套SELECT语句，称谓嵌套查询，又称子查询。
`SELECT * FROM t1 WHERE column1 = ( SELECT column1 FROM t2);`
**子查询外部的语句可以是 INSERT / UPDATE / DELETE / SELECT 的任何一个**

根据子查询结果可以分为：

- 标量子查询（子查询结果为单个值）
- 列子查询（子查询结果为一列）
- 行子查询（子查询结果为一行）
- 表子查询（子查询结果为多行多列）

根据子查询位置可分为：

- WHERE 之后
- FROM 之后
- SELECT 之后

#### 标量子查询

子查询返回的结果是单个值（数字、字符串、日期等）。
常用操作符：- < > > >= < <=

例子：

```mysql
-- 查询销售部所有员工
select id from dept where name = '销售部';
-- 根据销售部部门ID，查询员工信息
select * from employee where dept = 4;
-- 合并（子查询）
select * from employee where dept = (select id from dept where name = '销售部');

-- 查询xxx入职之后的员工信息
select * from employee where entrydate > (select entrydate from employee where name = 'xxx');
```

#### 列子查询

返回的结果是一列（可以是多行）。

常用操作符：

| 操作符  | 描述  |
| ------------ | ------------ |
| IN  | 在指定的集合范围内，多选一  |
| NOT IN  | 不在指定的集合范围内  |
| ANY  | 子查询返回列表中，有任意一个满足即可  |
| SOME  | 与ANY等同，使用SOME的地方都可以使用ANY  |
| ALL  | 子查询返回列表的所有值都必须满足  |

例子：

```mysql
-- 查询销售部和市场部的所有员工信息
select * from employee where dept in (select id from dept where name = '销售部' or name = '市场部');
-- 查询比财务部所有人工资都高的员工信息
select * from employee where salary > all(select salary from employee where dept = (select id from dept where name = '财务部'));
-- 查询比研发部任意一人工资高的员工信息
select * from employee where salary > any (select salary from employee where dept = (select id from dept where name = '研发部'));
```

#### 行子查询

返回的结果是一行（可以是多列）。
常用操作符：=, <, >, IN, NOT IN

例子：

```mysql
-- 查询与xxx的薪资及直属领导相同的员工信息
select * from employee where (salary, manager) = (12500, 1);
select * from employee where (salary, manager) = (select salary, manager from employee where name = 'xxx');
```

#### 表子查询

返回的结果是多行多列
常用操作符：IN

例子：

```mysql
-- 查询与xxx1，xxx2的职位和薪资相同的员工
select * from employee where (job, salary) in (select job, salary from employee where name = 'xxx1' or name = 'xxx2');
-- 查询入职日期是2006-01-01之后的员工，及其部门信息
select e.*, d.* from (select * from employee where entrydate > '2006-01-01') as e left join dept as d on e.dept = d.id;
```

## 事务

事务是一组操作的集合，事务会把所有操作作为一个整体一起向系统提交或撤销操作请求，即这些操作要么同时成功，要么同时失败。

基本操作：

```mysql
-- 1. 查询张三账户余额
select * from account where name = '张三';
-- 2. 将张三账户余额-1000
update account set money = money - 1000 where name = '张三';
-- 此语句出错后张三钱减少但是李四钱没有增加
模拟sql语句错误
-- 3. 将李四账户余额+1000
update account set money = money + 1000 where name = '李四';

-- 查看事务提交方式
SELECT @@AUTOCOMMIT;
-- 设置事务提交方式，1为自动提交，0为手动提交，该设置只对当前会话有效
SET @@AUTOCOMMIT = 0;
-- 提交事务
COMMIT;
-- 回滚事务
ROLLBACK;

-- 设置手动提交后上面代码改为：
select * from account where name = '张三';
update account set money = money - 1000 where name = '张三';
update account set money = money + 1000 where name = '李四';
commit;
```

操作方式二：

开启事务：
`START TRANSACTION 或 BEGIN TRANSACTION;`
提交事务：
`COMMIT;`
回滚事务：
`ROLLBACK;`

操作实例：

```mysql
start transaction;
select * from account where name = '张三';
update account set money = money - 1000 where name = '张三';
update account set money = money + 1000 where name = '李四';
commit;
```

### 四大特性ACID

- 原子性(Atomicity)：事务是不可分割的最小操作但愿，要么全部成功，要么全部失败
- 一致性(Consistency)：事务完成时，必须使所有数据都保持一致状态
- 隔离性(Isolation)：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行
- 持久性(Durability)：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的

### 并发事务

| 问题  | 描述  |
| ------------ | ------------ |
| 脏读  | 一个事务读到另一个事务还没提交的数据  |
| 不可重复读  | 一个事务先后读取同一条记录，但两次读取的数据不同  |
| 幻读  | 一个事务按照条件查询数据时，没有对应的数据行，但是再插入数据时，又发现这行数据已经存在  |

> 这三个问题的详细演示：https://www.bilibili.com/video/BV1Kr4y1i7ru?p=55cd 

并发事务隔离级别：

| 隔离级别  | 脏读  | 不可重复读  | 幻读  |
| ------------ | ------------ | ------------ | ------------ |
| Read uncommitted  | √  | √  | √  |
| Read committed  | ×  | √  | √  |
| Repeatable Read(默认)  | ×  | ×  | √  |
| Serializable  | ×  | ×  | ×  |

- √表示在当前隔离级别下该问题会出现
- Serializable 性能最低；Read uncommitted 性能最高，数据安全性最差

查看事务隔离级别：
`SELECT @@TRANSACTION_ISOLATION;`
设置事务隔离级别：
`SET [ SESSION | GLOBAL ] TRANSACTION ISOLATION LEVEL {READ UNCOMMITTED | READ COMMITTED | REPEATABLE READ | SERIALIZABLE };`
SESSION 是会话级别，表示只针对当前会话有效，GLOBAL 表示对所有会话有效















# 进阶篇

## 一 存储引擎

### 1 MySQL体系结构：

![结构图](https://learning-logs-1253130399.cos.ap-guangzhou.myqcloud.com/editor/MySQL体系结构_20220315034329549927.png "结构图")
![层级描述](https://learning-logs-1253130399.cos.ap-guangzhou.myqcloud.com/editor/MySQL体系结构层级含义_20220315034359342837.png "层级描述")

**<font color='red'>存储引擎</font>**就是**存储数据、建立索引、更新/查询数据**等技术的实现方式。存储引擎是基于表而不是基于库的，所以存储引擎也可以被称为**表引擎**。
Mysql的默认存储引擎是InnoDB。

相关操作：

```mysql
-- 查询建表语句
show create table account;
-- 建表时指定存储引擎
CREATE TABLE 表名(
	...
) ENGINE=INNODB;
-- 查看当前数据库支持的存储引擎
show engines;
```

#### （1）InnoDB

InnoDB 是一种兼顾高可靠性和高性能的通用存储引擎，在 MySQL 5.5 之后，InnoDB 是默认的 MySQL 引擎。

> **特点**

- DML 操作遵循 ACID 模型，支持**<font color='red'>事务</font>**
- **<font color='red'>行</font>级锁**，提高并发访问性能
- 支持**<font color='red'>外键</font>**约束，保证数据的完整性和正确性

> **InnoDB引擎的数据表包含的文件**

- **<font color='red'>xxx.ibd</font>**: xxx代表表名，InnoDB 引擎的每张表都会对应这样一个**表空间文件**，存储该表的**表结构（frm、sdi）**、**数据**和**索引**。

系统变量：innodb_file_per_table，决定多张表共享一个表空间还是每张表对应一个表空间

知识点：

查看 Mysql 变量：
`show variables like 'innodb_file_per_table';`

从idb文件提取表结构数据：

（在cmd运行）

`ibd2sdi xxx.ibd`

InnoDB 逻辑存储结构：
![InnoDB逻辑存储结构](https://learning-logs-1253130399.cos.ap-guangzhou.myqcloud.com/editor/逻辑存储结构_20220316030616590001.png "InnoDB逻辑存储结构")

[InnoDB(4.1)：InnoDB的逻辑存储结构简介（表、段、区、页、行）_innodb存储引擎是面向列的(row-oriented),也就是说数据是按行进行存放的-CSDN博客](https://blog.csdn.net/GDUT_Trim/article/details/115982884)

表空间是由各个段组成的，常见的段有数据段、索引段、回滚段等。区的大小固定为1M，页的大小默认为16kb。

**数据段其实就是InnoDB索引底层实现结构的B+树的叶子结点（leaf node segment）,索引段即为B+树的非叶子结点（Non-leaf node segement）**

#### （2）MyISAM

MyISAM 是 MySQL 早期的默认存储引擎。

> 特点

- 不支持**事务**，不支持**外键**
- 支持**<font color='red'>表锁</font>**，不支持行锁
- 访问速度快

> MyISAM引擎的数据表包含的文件

- xxx.sdi: 存储表结构信息
- xxx.MYD: 存储数据
- xxx.MYI: 存储索引

#### （3）Memory

Memory 引擎的表数据是存储在**<font color='red'>内存</font>**中的，受硬件问题、断电问题的影响，只能将这些表作为**临时表或缓存使用**。

特点：

- 存放在内存中，速度快
- **<font color='red'>hash索引</font>**（默认）

文件：

- xxx.sdi: 存储表结构信息

### 2 存储引擎特点

| 特点  | InnoDB  | MyISAM  | Memory  |
| ------------ | ------------ | ------------ | ------------ |
| 存储限制  | 64TB  | 有  | 有  |
| 事务安全  | 支持  | -  | -  |
| 锁机制  | 行锁  | 表锁  | 表锁  |
| B+tree索引  | 支持  | 支持  | 支持  |
| Hash索引  | -  | -  | 支持  |
| 全文索引  | 支持（5.6版本之后）  | 支持  | -  |
| 空间使用  | 高  | 低  | N/A  |
| 内存使用  | 高  | 低  | 中等  |
| 批量插入速度  | 低  | 高  | 高  |
| 支持外键  | 支持  | -  | -  |

### 3 存储引擎的选择

在选择存储引擎时，应该根据应用系统的特点选择合适的存储引擎。对于复杂的应用系统，还可以根据实际情况选择多种存储引擎进行组合。

- InnoDB: 如果应用对事务的完整性有比较高的要求，在并发条件下要求数据的一致性，数据操作除了插入和查询之外，还包含很多的更新、删除操作，则 InnoDB 是比较合适的选择
- MyISAM: 如果应用是以**读操作和插入**操作为主，只有很少的更新和删除操作，并且对事务的完整性、并发性要求不高，那这个存储引擎是非常合适的。
- Memory: 将所有数据保存在内存中，访问速度快，通常用于临时表及缓存。Memory 的缺陷是对表的大小有限制，太大的表无法缓存在内存中，而且无法保障数据的安全性

电商中的足迹和评论适合使用 MyISAM 引擎，缓存适合使用 Memory 引擎。

## 二 性能分析

### 1 SQL执行频次

查看当前数据库的 INSERT, UPDATE, DELETE, SELECT 访问频次：
`SHOW GLOBAL STATUS LIKE 'Com_______';` 或者 `SHOW SESSION STATUS LIKE 'Com_______';`
例：`show global status like 'Com_______'`

### 2 慢查询日志

慢查询日志记录了所有执行时间超过指定参数（long_query_time，单位：秒，默认10秒）的所有SQL语句的日志。
MySQL的慢查询日志默认没有开启，需要在MySQL的配置文件（/etc/my.cnf）中配置如下信息：

```properties
# 开启慢查询日志开关
slow_query_log=1
# 设置慢查询日志的时间为2秒，SQL语句执行时间超过2秒，就会视为慢查询，记录慢查询日志
long_query_time=2
```

更改后记得重启MySQL服务，日志文件位置：/var/lib/mysql/localhost-slow.log

查看慢查询日志开关状态：
`show variables like 'slow_query_log';`

### 3 profile

show profile 能在做SQL优化时帮我们了解**时间都耗费在什么步骤上**。通过 have_profiling 参数，能看到当前 MySQL 是否支持 profile 操作：

`SELECT @@have_profiling;`

profiling 默认关闭，可以通过set语句在session/global级别开启 profiling：

`SET profiling = 1;`

查看所有语句的耗时：

`show profiles;`

查看指定query_id的SQL语句各个阶段的耗时：

`show profile for query query_id;`

查看指定query_id的SQL语句CPU的使用情况

`show profile cpu for query query_id;`

### 4 explain

EXPLAIN 或者 DESC 命令获取 MySQL **如何执行 SELECT 语句的信息**，包括在 SELECT 语句执行过程中表如何连接和连接的顺序。

语法：

**直接在select语句之前加上关键字 explain / desc**

```mysql
EXPLAIN SELECT 字段列表 FROM 表名 HWERE 条件;
```

EXPLAIN 各字段含义：

- id：select 查询的序列号，表示查询中执行 select 子句或者操作表的顺序（id相同，执行顺序从上到下；id不同，值越大越先执行）
- select_type：表示 SELECT 的类型，常见取值有 SIMPLE（简单表，即不适用表连接或者子查询）、PRIMARY（主查询，即外层的查询）、UNION（UNION中的第二个或者后面的查询语句）、SUBQUERY（SELECT/WHERE之后包含了子查询）等
- type：表示连接类型，性能由好到差的连接类型为 NULL、system、const、eq_ref、ref、range、index、all
- possible_key：可能应用在这张表上的索引，一个或多个
- Key：实际使用的索引，如果为 NULL，则没有使用索引
- Key_len：表示索引中使用的字节数，该值为索引字段最大可能长度，并非实际使用长度，在不损失精确性的前提下，长度越短越好
- rows：MySQL认为必须要执行的行数，在InnoDB引擎的表中，是一个估计值，可能并不总是准确的
- filtered：表示返回结果的行数占需读取行数的百分比，filtered的值越大越好

## 三 索引

### 1 索引概述

索引是帮助 MySQL **高效获取数据**的**数据结构（有序）**。在数据之外，数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用（指向）数据，这样就可以在这些数据结构上实现高级查询算法，这种数据结构就是索引。

优缺点：

优点：

- 提高数据检索效率，降低数据库的IO成本
- 通过索引列对数据进行排序，降低数据排序的成本，降低CPU的消耗

缺点：

- 索引列也是要占用空间的
- 索引大大提高了查询效率，但降低了更新的速度，比如 INSERT、UPDATE、DELETE

### 2 索引结构

| 索引结构  | 描述  |
| ------------ | ------------ |
| B+Tree  | 最常见的索引类型，大部分引擎都支持B+树索引  |
| Hash  | 底层数据结构是用哈希表实现，只有精确匹配索引列的查询才有效，不支持范围查询  |
| R-Tree(空间索引)  | 空间索引是 MyISAM 引擎的一个特殊索引类型，主要用于地理空间数据类型，通常使用较少  |
| Full-Text(全文索引)  | 是一种通过建立倒排索引，快速匹配文档的方式，类似于 Lucene, Solr, ES  |

| 索引  | InnoDB  | MyISAM  | Memory  |
| ------------ | ------------ | ------------ | ------------ |
| B+Tree索引  | 支持  | 支持  | 支持  |
| Hash索引  | 不支持  | 不支持  | 支持  |
| R-Tree索引  | 不支持  | 支持  | 不支持  |
| Full-text  | 5.6版本后支持  | 支持  | 不支持  |

#### B-Tree

![二叉树](https://learning-logs-1253130399.cos.ap-guangzhou.myqcloud.com/editor/二叉树_20220316153214227108.png "二叉树")

二叉树的缺点可以用红黑树来解决：
![红黑树](https://learning-logs-1253130399.cos.ap-guangzhou.myqcloud.com/editor/红黑树_20220316163142686602.png "红黑树")
红黑树也存在大数据量情况下，层级较深，检索速度慢的问题。

为了解决上述问题，可以使用 B-Tree 结构。
B-Tree (多路平衡查找树) 以一棵最大度数（max-degree，指一个节点的子节点个数）为5（5阶）的 b-tree 为例（每个节点最多存储4个key，5个指针）

![B-Tree结构](https://learning-logs-1253130399.cos.ap-guangzhou.myqcloud.com/editor/B-Tree结构_20220316163813441163.png "B-Tree结构")

> B-Tree 的数据插入过程动画参照：https://www.bilibili.com/video/BV1Kr4y1i7ru?p=68
演示地址：https://www.cs.usfca.edu/~galles/visualization/BTree.html

#### B+Tree

结构图：

![B+Tree结构图](https://learning-logs-1253130399.cos.ap-guangzhou.myqcloud.com/editor/B+Tree结构图_20220316170700591277.png "B+Tree结构图")

> 演示地址：https://www.cs.usfca.edu/~galles/visualization/BPlusTree.html

与 B-Tree 的区别：

- 所有的数据都会出现在叶子节点
- 叶子节点形成一个单向链表

MySQL 索引数据结构对经典的 B+Tree 进行了优化。在原 B+Tree 的基础上，增加一个指向相邻叶子节点的链表指针，就形成了带有顺序指针的 B+Tree，提高区间访问的性能。

![MySQL B+Tree 结构图](https://learning-logs-1253130399.cos.ap-guangzhou.myqcloud.com/editor/结构图_20220316171730865611.png "MySQL B+Tree 结构图")

磁盘空间按页对其分配。B+ 树非叶子节点不存放数据，磁盘的每一页能容纳更多的键指针，这样在查找时，磁盘IO的次数也会变少。

#### Hash

哈希索引就是采用一定的hash算法，将键值换算成新的hash值，映射到对应的槽位上，然后存储在hash表中。
如果两个（或多个）键值，映射到一个相同的槽位上，他们就产生了hash冲突（也称为hash碰撞），可以通过链表来解决。

![Hash索引原理图](https://learning-logs-1253130399.cos.ap-guangzhou.myqcloud.com/editor/Hash索引原理图_20220317143226150679.png "Hash索引原理图")

特点：

- Hash索引只能用于对等比较（=、in），不支持范围查询（betwwn、>、<、...）
- 无法利用索引完成排序操作
- 查询效率高，通常只需要一次检索就可以了，效率通常要高于 B+Tree 索引

存储引擎支持：

- Memory
- InnoDB: 具有自适应hash功能，hash索引是存储引擎根据 B+Tree 索引在指定条件下自动构建的

#### 面试题

1. 为什么 InnoDB 存储引擎选择使用 B+Tree 索引结构？

- 相对于二叉树，层级更少，搜索效率高
- 对于 B-Tree，无论是叶子节点还是非叶子节点，都会保存数据，这样导致一页中存储的键值减少，指针也跟着减少，要同样保存大量数据，只能增加树的高度，导致性能降低
- 相对于 Hash 索引，B+Tree 支持范围匹配及排序操作

### 3 索引分类

| 分类  | 含义  | 特点  | 关键字  |
| ------------ | ------------ | ------------ | ------------ |
| 主键索引  | 针对于表中主键创建的索引  | 默认自动创建，只能有一个  | PRIMARY  |
| 唯一索引  | 避免同一个表中某数据列中的值重复  | 可以有多个  | UNIQUE  |
| 常规索引  | 快速定位特定数据  | 可以有多个  |   |
| 全文索引  | 全文索引查找的是文本中的关键词，而不是比较索引中的值  | 可以有多个  | FULLTEXT  |

在 InnoDB 存储引擎中，根据索引的存储形式，又可以分为以下两种：

| 分类  | 含义  | 特点  |
| ------------ | ------------ | ------------ |
| 聚集索引(Clustered Index)  | 将数据存储与索引放一块，索引结构的叶子节点保存了行数据  | 必须有，而且只有一个  |
| 二级索引(Secondary Index)  | 将数据与索引分开存储，索引结构的叶子节点关联的是对应的主键  | 可以存在多个  |

演示图：

![大致原理](https://learning-logs-1253130399.cos.ap-guangzhou.myqcloud.com/editor/原理图_20220318194454880073.png "大致原理")
![演示图](https://learning-logs-1253130399.cos.ap-guangzhou.myqcloud.com/editor/演示图_20220319215403721066.png "演示图")

聚集索引选取规则：

- 如果存在主键，主键索引就是聚集索引
- 如果不存在主键，将使用第一个唯一(UNIQUE)索引作为聚集索引
- 如果表没有主键或没有合适的唯一索引，则 InnoDB 会自动生成一个 rowid 作为隐藏的聚集索引

#### 思考题

1\. 以下 SQL 语句，哪个执行效率高？为什么？

```mysql
select * from user where id = 10;
select * from user where name = 'Arm';
-- 备注：id为主键，name字段创建的有索引
```

答：第一条语句，因为第二条需要回表查询，相当于两个步骤。

2\. InnoDB 主键索引的 B+Tree 高度为多少？

答：假设一行数据大小为1k，一页中可以存储16行这样的数据。InnoDB 的指针占用6个字节的空间，主键假设为bigint，占用字节数为8.
可得公式：`n * 8 + (n + 1) * 6 = 16 * 1024`，其中 8 表示 bigint 占用的字节数，n 表示当前节点存储的key的数量，(n + 1) 表示指针数量（比key多一个）。算出n约为1170。

如果树的高度为2，那么他能存储的数据量大概为：`1171 * 16 = 18736`；
如果树的高度为3，那么他能存储的数据量大概为：`1171 * 1171 * 16 = 21939856`。

另外，如果有成千上万的数据，那么就要考虑分表，涉及运维篇知识。

### 4 语法

1. 创建索引：
   `CREATE [ UNIQUE | FULLTEXT ] INDEX index_name ON table_name (index_col_name, ...);`
   如果不加 CREATE 后面不加索引类型参数，则创建的是常规索引

2. 查看索引：
   `SHOW INDEX FROM table_name;`

3. 删除索引：
   `DROP INDEX index_name ON table_name;`

案例：

```mysql
-- name字段为姓名字段，该字段的值可能会重复，为该字段创建索引
create index idx_user_name on tb_user(name);
-- phone手机号字段的值非空，且唯一，为该字段创建唯一索引
create unique index idx_user_phone on tb_user(phone);
-- 为profession, age, status创建联合索引
create index idx_user_pro_age_stat on tb_user(profession, age, status);
-- 为email建立合适的索引来提升查询效率
create index idx_user_email on tb_user(email);

-- 删除索引
drop index idx_user_email on tb_user;
```

### 5 使用规则

#### (1) 最左前缀法则

如果索引关联了多列（**联合索引**），要遵守最左前缀法则，最左前缀法则指的是查询从索引的最左列开始，并且不跳过索引中的列。
如果跳跃某一列，索引将部分失效（后面的字段索引失效）。

联合索引中，出现范围查询（<, >），范围查询右侧的列索引失效。可以用>=或者<=来规避索引失效问题。

#### (2) 索引失效情况

1. 在索引列上进行运算操作，索引将失效。如：`explain select * from tb_user where substring(phone, 10, 2) = '15';`

2. 字符串类型字段使用时，不加引号，索引将失效。如：`explain select * from tb_user where phone = 17799990015;`，此处phone的值没有加引号。

   实际上是字符串和数字做比较时，MySQL会（隐式类型转换）将字符串转为数字，这里相当于把索引（phone是varchar）做了cast强转（函数），所以失效了。

   相反，如果是id="1"就不会失效。

3. 模糊查询中，如果仅仅是尾部模糊匹配，索引不会是失效；如果是头部模糊匹配，索引失效。如：`explain select * from tb_user where profession like '%工程';`，前后都有 % 也会失效。

   EXPLAIN SELECT * FROM t_user WHERE age LIKE '2%';这个索引也会失效。

4. 用 or 分割开的条件，如果 or 其中一个条件的列没有索引，那么涉及的索引都不会被用到。

5. 如果 MySQL 评估使用索引比全表更慢，则不使用索引。

#### (3) SQL 提示

是优化数据库的一个重要手段，简单来说，就是在SQL语句中加入一些人为的提示来达到优化操作的目的。

例如，使用索引：
`explain select * from tb_user use index(idx_user_pro) where profession="软件工程";`
不使用哪个索引：
`explain select * from tb_user ignore index(idx_user_pro) where profession="软件工程";`
必须使用哪个索引：
`explain select * from tb_user force index(idx_user_pro) where profession="软件工程";`

use 是建议，实际使用哪个索引 MySQL 还会自己权衡运行速度去更改，force就是无论如何都强制使用该索引。

#### (4) 覆盖索引&回表查询

尽量使用覆盖索引（查询使用了索引，并且需要返回的列，在该索引中已经全部能找到），减少 select *。

explain 中 extra 字段含义：
`using index condition`：查找使用了索引，但是需要回表查询数据
`using where; using index;`：查找使用了索引，但是需要的数据都在索引列中能找到，所以不需要回表查询

如果在聚集索引中直接能找到对应的行，则直接返回行数据，只需要一次查询，哪怕是select \*；如果在辅助索引中找聚集索引，如`select id, name from xxx where name='xxx';`，也只需要通过辅助索引(name)查找到对应的id，返回name和name索引对应的id即可，只需要一次查询；如果是通过辅助索引查找其他字段，则需要回表查询，如`select id, name, gender from xxx where name='xxx';`

所以尽量不要用`select *`，容易出现回表查询，降低效率，除非有联合索引包含了所有字段

面试题：一张表，有四个字段（id, username, password, status），由于数据量大，需要对以下SQL语句进行优化，该如何进行才是最优方案：
`select id, username, password from tb_user where username='itcast';`

解：给username和password字段建立联合索引，则不需要回表查询，直接覆盖索引

#### (5) 前缀索引

当字段类型为字符串（varchar, text等）时，有时候需要索引很长的字符串，这会让索引变得很大，查询时，浪费大量的磁盘IO，影响查询效率，此时可以只降字符串的一部分前缀，建立索引，这样可以大大节约索引空间，从而提高索引效率。

语法：`create index idx_xxxx on table_name(columnn(n));`
前缀长度：可以根据索引的选择性来决定，而选择性是指不重复的索引值（基数）和数据表的记录总数的比值，索引选择性越高则查询效率越高，唯一索引的选择性是1，这是最好的索引选择性，性能也是最好的。
求选择性公式：
```mysql
select count(distinct email) / count(*) from tb_user;
select count(distinct substring(email, 1, 5)) / count(*) from tb_user;
```

show index 里面的sub_part可以看到接取的长度

#### (6) 单列索引&联合索引

单列索引：即一个索引只包含单个列
联合索引：即一个索引包含了多个列
在业务场景中，如果存在多个查询条件，考虑针对于查询字段建立索引时，建议建立联合索引，而非单列索引。

单列索引情况：
`explain select id, phone, name from tb_user where phone = '17799990010' and name = '韩信';`
这句只会用到phone索引字段

##### 注意事项

- 多条件联合查询时，MySQL优化器会评估哪个字段的索引效率更高，会选择该索引完成本次查询

### 6 索引的设计原则

1. 针对于数据量较大，且查询比较频繁的表建立索引
2. 针对于常作为查询条件（where）、排序（order by）、分组（group by）操作的字段建立索引
3. 尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高
4. 如果是字符串类型的字段，字段长度较长，可以针对于字段的特点，建立前缀索引
5. 尽量使用联合索引，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间，避免回表，提高查询效率
6. 要控制索引的数量，索引并不是多多益善，索引越多，维护索引结构的代价就越大，会影响增删改的效率
7. 如果索引列不能存储NULL值，请在创建表时使用NOT NULL约束它。当优化器知道每列是否包含NULL值时，它可以更好地确定哪个索引最有效地用于查询

## 四 SQL 优化

### 插入数据

采用普通插入时建议：

1. 采用批量插入（一次插入的数据不建议超过1000条）
2. 手动提交事务，关闭自动提交避免频繁开启和提交
3. 主键顺序插入，顺序插入的效率大于乱序插入

大批量插入：
如果一次性需要插入大批量数据，使用insert语句插入性能较低，此时可以使用MySQL数据库提供的load指令插入。

```mysql
# 客户端连接服务端时，加上参数 --local-infile（这一行在bash/cmd界面输入）
mysql --local-infile -u root -p
# 设置全局参数local_infile为1，开启从本地加载文件导入数据的开关
set global local_infile = 1;
select @@local_infile;
# 执行load指令将准备好的数据，加载到表结构中
load data local infile '/root/sql1.log' into table 'tb_user' fields terminated by ',' lines terminated by '\n';
```

### 主键优化

数据组织方式：在InnoDB存储引擎中，表数据都是根据主键顺序组织存放的，这种存储方式的表称为索引组织表（Index organized table, IOT）

**页分裂：**页可以为空，也可以填充一半，也可以填充100%，每个页包含了2-N行数据（如果只有1行，那就变成链表了，如果一行数据过大，会行溢出），根据主键排列。

在随机插入行数据 row N时，根据 id 要插入的位置所在的页 page N 已经满了，此时会新开辟一个页 page M ，把已经满了的页 page N 的后一半数据存入，再把要插进的数据 插入到 page N 中，随后将页的链表指针重排，把 page M 接在 page N 后面，记为 page N+1，原先 page N+1 页 后移，记作 page N+2。

**页合并：**当删除一行记录时，实际上记录并没有被物理删除，只是记录被标记（flaged）为删除并且它的空间变得允许被其他记录声明使用。当页中删除的记录到达 MERGE_THRESHOLD（默认为页的50%），InnoDB会开始寻找最靠近的页（前后）看看是否可以将这两个页合并以优化空间使用。如若后面的数据页数据少于50%，会把后面数据页的数据填入当前页。

MERGE_THRESHOLD：合并页的阈值，可以自己设置，在创建表或创建索引时指定

> 文字说明不够清晰明了，具体可以看视频里的PPT演示过程：https://www.bilibili.com/video/BV1Kr4y1i7ru?p=90

主键设计原则：

- 满足业务需求的情况下，尽量降低主键的长度。二级索引下挂着的是主键，二级索引较多时，会造成较多的空间浪费。
- 插入数据时，尽量选择顺序插入，选择使用 AUTO_INCREMENT 自增主键。乱序插入可能造成页分裂
- 尽量不要使用 UUID 做主键或者是其他的自然主键，如身份证号。因为UUID是无序的，在插入时，是乱序插入，而且UUID一般较长。
- 业务操作时，避免对主键的修改

### order by优化

1. Using filesort：通过表的索引或全表扫描，读取满足条件的数据行，然后**在排序缓冲区 sort buffer 中完成排序操作**，所有**不是通过索引直接返回排序结果的排序**都叫 FileSort 排序
2. Using index：通过有序索引顺序扫描**直接**返回有序数据，这种情况即为 using index，**不需要额外排序**，操作效率高（尽量优化成using index）

异常情况：

① 索引默认升序排列 asc 。如果排序方式与索引相反会提示**反向扫描索引 Backward index scan**; Using index（相对 using filesort 来说影响不大）

② 有两个字段的排序，如果与联合索引的顺序不一致会出现 Using index; Using filesort 提示，因为违反了**最左前缀原则**。

③ 如果order by字段全部使用升序排序或者降序排序，(不管索引的collection是asc还是desc）都会走索引，但是如果**一个字段升序排序，另一个字段降序排序**，则不会走索引，explain的extra信息显示的是`Using index, Using filesort`，如果要优化掉Using filesort，则需要另外再创建一个索引，如：`create index idx_user_age_phone_ad on tb_user(age asc, phone desc);`，此时使用`select id, age, phone from tb_user order by age asc, phone desc;`会全部走索引

总结：

- 根据排序字段建立合适的索引，多字段排序时，也遵循最左前缀法则
- 尽量使用覆盖索引，没有覆盖则会返回 using filesort
- 多字段排序，一个升序一个降序，此时需要注意联合索引在创建时的规则（ASC/DESC）
- 如果不可避免出现filesort，大数据量排序时，可以适当增大排序缓冲区大小 sort_buffer_size（默认256k）

### group by优化

- 在分组操作时，可以通过索引来提高效率
- 分组操作时，索引的使用也是满足**最左前缀法则**的

异常情况：

分组时，没有使用索引，extra会反馈创建了**临时表 Using temporary**

索引为 idx_user_pro_age_sta，而 group by 使用 age 时不满足最左前缀法则，会提示 Using index; Using temporary。

如索引为`idx_user_pro_age_stat`，则句式可以是`select ... where profession order by age`，这样也符合最左前缀法则

### limit优化

常见的问题如`order by id limit 2000000, 10`，此时需要 MySQL 排序前2000000条记录，但仅仅返回2000000 - 2000010的记录，其他记录丢弃，查询排序的代价非常大。**越往后，性能越低。**
优化方案：一般分页查询时，通过创建覆盖索引能够比较好地提高性能，可以通过**覆盖索引加子查询**形式进行优化

例如：

```mysql
-- 此语句耗时很长
select * from tb_sku limit 9000000, 10;
-- 通过覆盖索引加快速度，直接通过主键索引进行排序及查询
select id from tb_sku order by id limit 9000000, 10;
-- 下面的语句是错误的，因为 MySQL 不支持 in 里面使用 limit
-- select * from tb_sku where id in (select id from tb_sku order by id limit 9000000, 10);
-- 通过连表查询即可实现第一句的效果，并且能达到第二句的速度
select * from tb_sku as s, (select id from tb_sku order by id limit 9000000, 10) as a where s.id = a.id;
```

### count优化

MyISAM 引擎把一个表的总行数存在了磁盘上，因此执行 count(\*) 的时候会直接返回这个数，效率很高（**前提是不使用where的筛选条件**）；
InnoDB 在执行 count(\*) 时，需要把数据一行一行地从引擎里面读出来，然后累计计数。所以count在优化时本身就是比较有限的。
优化方案：除非能够自己计数，如创建key-value表存储在内存或硬盘，或者是用redis。每加一条数据 +1，删一条数据 -1，自己来维护。

count的几种用法：

- 如果count函数的参数（count里面写的那个字段）不是NULL（字段值不为NULL），累计值就加一，最后返回累计值
- 用法：count(\*)、count(主键)、count(字段)、count(1)
- count(主键)跟count(\*)一样，因为主键不能为空；count(字段)只计算字段值不为NULL的行；count(1)引擎会为每行添加一个1，然后就count这个1，返回结果也跟count(\*)一样；count(null)返回0

各种用法的性能：

- count(主键)：InnoDB引擎会遍历整张表，**把每行的主键id值都取出来**，返回给服务层，服务层拿到主键后，**直接**按行进行累加（主键不可能为空）
- count(字段)：**没有not null约束**的话，InnoDB引擎会遍历整张表**把每一行的字段值都取出来**，返回给服务层，服务层**判断是否为null**，不为null，计数累加；**有not null约束**的话，InnoDB引擎会遍历整张表把每一行的字段值都取出来，返回给服务层，**直接**按行进行累加
- count(1)：InnoDB 引擎遍历整张表，但**不取值**。服务层对于返回的每一层，放一个数字 1 进去，**直接**按行进行累加。除了1，可以是任何数，最后返回的都是总行数。
- count(\*)：InnoDB 引擎并不会把全部字段取出来，而是专门做了优化，**不取值**，服务层**直接**按行进行累加

按效率排序：**count(字段) < count(主键) < count(1) ≈/< count(\*)，所以尽量使用 count(\*)**

### update优化（避免行锁升级为表锁）

InnoDB 的行锁是针对索引加的锁，不是针对记录加的锁，并且该索引不能失效，否则会从**行锁**升级为**表锁**，这会降低数据库的并发访问性能。

如以下两条语句：
`update student set no = '123' where id = 1;`，这句由于id有主键索引，所以只会锁这一行；
`update student set no = '123' where name = 'test';`，这句由于name没有索引，所以会把整张表都锁住进行数据更新，解决方法是给name字段添加索引

后续内容

接着看这个博客





# 4 视图/存储过程/触发器

## 4.1 视图

### 4.1.1 介绍

1. 视图（View）是一种虚拟存在的表。
2. 视图中的数据并不在数据库中实际存在，行和列数据来自定义视图的查询中使用的表，并且是在使用视图时动态生成的。
3. 视图只保存了查询的SQL逻辑，不保存查询结果。所以对于创建视图的主要工作都在于创建这条SQL查询语句上。
4. 对视图的操作与表类似。

### 4.1.2 语法

- 创建

```mysql
CREATE [OR REPLACE] VIEW 视图名称[(列名列表)] AS SELECT语句 [WITH [
CASCADED|LOCAL] CHECK OPTION]

-- create view stu_v_1 as select id,name from student where id <= 10;
```

查看创建视图语句

```mysql
SHOW CREATE VIEW 视图名称;

-- show create view stu_v_1;
```

查看视图数据

```mysql
SELECT * FROM 视图名称 ...... ;

-- select * from stu_v_1;

-- select * from stu_v_1 where id < 3;
```

修改

```mysql
-- 方法1
ALTER VIEW 视图名称[(列名列表)] AS SELECT语句 [WITH [CASCADED|LOCAL] CHECK OPTION]
-- alter view stu_v_1 as select id,name from student where id <= 10;

-- 方法2
create or replace view 视图名称[(列名列表)] as select语句 [with [
cascaded|local] check option]
```

删除

```mysql
DROP VIEW IF EXISTS 视图名称 [,视图名称] ...

drop view if exists stu_v_1;
```

#### 4.1.2.1 插入、更新数据

```mysql
create or replace view stu_v_1 as select id,name from student where id <= 10 ;
-- 添加数据
insert into stu_v_1 values(6,'Tom');

insert into stu_v_1 values(17,'Tom22');
```

id为6和17的数据都是可以成功插入的，但是查询出来的数据，却没有id为17的记录，在创建视图的时候，指定的条件为 id<=10，id为17的数据，是不符合条件的，但是这条数据确实是已经成功的插入到了基表中。

### 4.1.3 检查选项

为应对以上的情况，使用==**WITH CHECK OPTION**==子句创建视图时，MySQL会通过视图检查正在更改的每个行，如果正在进行的操作不满足视图创建时的规则则会弹出“**通过检查选项失败 CHECK OPTION FAILED**”的提示信息。

mysql提供了两个选项： CASCADED 和 LOCAL，默认值为 CASCADED

#### 4.1.3.1 CASCADED：级联

v2视图是基于v1视图的
v2视图创建的时候指定了检查选项为 cascaded
在执行检查时，不仅会检查 v2 当前的条件是否满足，还会级联检查v2的关联视图v1

```mysql
create or replace view stu_v_1 as select id,name from student where id <=20;
create or replace view stu_v_2 as select id,name from stu_v_1 where id >10 with cascaded check option;
insert into stu_v_2 values(26,'Tom');-- 会报错
```

如果stu_v_2是由stu_v_1得到的，但是没加with cascaded check option，则不会关联检查

```mysql
create or replace view user_v_1 as select id,username from t_user where id <=20;
create or replace view user_v_2 as select id,username from user_v_1 where id >10;
create or replace view user_v_3 as select id,username from user_v_2 where id >15 WITH CASCADED CHECK OPTION;
INSERT INTO user_v_3 VALUES(23,'James');
-- 在view的继承关系上，从哪级开始有WITH CASCADED CHECK OPTION，就从哪级开始检查，一直查到基表，只要有一级检查不通过就返回CHECK OPTION failed '数据库名.user_v_3'
-- 即便当前要操作的视图并没有WITH CASCADED CHECK OPTION，也要往前找是否有
-- 如果检查通过，各级视图根据各自的数据范围确定是否添加数据（此时不管有没有WITH CASCADED CHECK OPTION，筛选条件都是叠加的），但原表一定会添加
```

cascade:英 /*kæˈskeɪd* *n./vi.* 级联 串联 倾泻

#### 4.1.3.2 LOCAL：本地

v2视图是基于v1视图的
v2视图创建的时候指定了检查选项为 local
执行检查时，知会检查v2，不会检查v2的关联视图v1

```mysql
create or replace view user_v_1 as select id,username from t_user where id <=20;
create or replace view user_v_2 as select id,username from user_v_1 where id >10;
create or replace view user_v_3 as select id,username from user_v_2 where id >15 WITH LOCAL CHECK OPTION;
INSERT INTO user_v_3 VALUES(23,'James');
-- 在view的继承关系上，哪级有WITH LOCAL CHECK OPTION，就检查哪级，只要有一级检查不通过就返回CHECK OPTION failed '数据库名.user_v_3'
-- 即便当前要操作的视图并没有WITH CASCADED CHECK OPTION，也要往前找是否有
-- 如果检查通过，各级视图根据各自的数据范围确定是否添加数据（此时不管有没有WITH LOCAL CHECK OPTION，筛选条件都是叠加的），但原表一定会添加。
-- 可能存在没有检查报错但没有一个view视图添加上了数据
```

### 4.1.4 视图的更新

要使视图可更新，视图中的行与基础表中的行之间必须存在**一对一**的关系，视图包含以下任何一项，则该视图不可更新（插入和更新）。
A. 聚合函数或窗口函数（SUM()、 MIN()、 MAX()、 COUNT()等）
B. DISTINCT
C. GROUP BY
D. HAVING
E. UNION 或者 UNION ALL

### 4.1.5 视图作用

**简单**：可以简化用户的操作，经常使用的查询可以定义为视图，使得用户**不必为以后的操作每次指定全部的条件**。如把较复杂的多表联查保存为视图。
**安全**：通过视图，用户只能查询和修改**（由管理员设计好的）**他们所能见到的**数据**。如整个表只对外暴露部分学生的姓名和id，提高**敏感数据**的安全性。
**数据独立**：视图可帮助用户屏蔽真实表结构变化带来的影响。若基表的字段改名了，这时视图可以把新的字段名起个原字段名的别名来规避影响。

### 4.1.6 案例

为了保证数据库表的安全性，开发人员在操作tb_user表时，只能看到的用户的基本字段，屏蔽手机号和邮箱两个字段

```mysql
create view tb_user_view 
as select id,name,profession,age,gender,status,createtime 
from tb_user;
```


查询每个学生所选修的课程（三张表联查），这个功能在很多的业务中都有使用到，为了简化操作，定义一个视图

```mysql
create view tb_stu_course_view 
as select s.name student_name, s.no student_no,c.name course_name 
from student s, student_course sc , course c 
where s.id = sc.studentid and sc.courseid = c.id;
```




## 4.2 存储过程

### 4.2.1 介绍

**存储过程**是事先经过编译并存储在数据库中的一段 SQL 语句的集合（**类似Java的函数**）。存储过程思想上很简单，就是数据库 SQL 语言层面的代码封装与重用
**特点：**封装，复用、可以接收参数，也可以返回数据、减少网络交互，效率提升。

### 4.2.2 基本语法

1. 创建

```mysql
CREATE PROCEDURE 存储过程名称 ([ 参数列表 ])
BEGIN
-- SQL语句
END ;

-- 注意分号的位置上
create procedure p1()
begin
   select count(*) from student;
end;
```

2. 调用

```mysql
CALL 名称 ([ 参数 ]);

-- 记得加括号
call p1();
```

3. 查看

```mysql
-- 查询指定数据库的存储过程及状态信息
SELECT * FROM INFORMATION_SCHEMA.ROUTINES WHERE ROUTINE_SCHEMA = 'xxx'; 

select * from information_schema.ROUTINES where ROUTINE_SCHEMA = 'itcast';

-- 查询某个存储过程的定义
SHOW CREATE PROCEDURE 存储过程名称 ; 

show create procedure p1;
```

4. 删除

```mysql
DROP PROCEDURE IF EXISTS 存储过程名称

drop procedure if exists p1;
```

==**注意**==：在命令行中，执行创建存储过程的SQL语句时，需要通过关键字**delimiter**指定SQL语句的结束符。（在命令行中，分号代表该语句结束，这不符合存储过程的定义规则。

```mysql
delimiter $$
create procedure p1()
begin
   select count(*) from student;
end$$
delimiter ;
```

### 4.2.3 变量

在MySQL中变量分为三种类型: 系统变量、用户定义变量、局部变量

#### 4.2.3.1 系统变量

系统变量 是MySQL服务器提供，不是用户定义的，分为全局变量（GLOBAL）、会话变量（SESSION）

- 查看系统变量

```mysql
-- 查看所有系统变量
SHOW [ SESSION | GLOBAL ] VARIABLES ; 
show session variables ;
show global variables ;
```

```mysql
-- 可以通过LIKE模糊匹配方式查找变量
SHOW [ SESSION | GLOBAL ] VARIABLES LIKE '......'; 
show session variables like 'auto%';
show global variables like 'auto%';
```

```mysql
-- 查看指定变量的值（如果你知道这个变量的全名）
SELECT @@[SESSION | GLOBAL].系统变量名; 
select @@global.autocommit;
select @@session.autocommit;

-- session可省略，默认session，global不可省略
```

- 设置系统变量

```mysql
SET [ SESSION | GLOBAL ] 系统变量名 = 值 ;
set session autocommit = 1;
set global autocommit = 0;
```

1. 如果没有指定SESSION/GLOBAL，**默认**是SESSION，会话变量

2. mysql 服务重新启动之后，所设置的全局参数会**失效**，要想不失效，可以在系统配置文件 **/etc/my.cnf** 中配置

   ```mysql
   -- 重启MySQL的命令
   systemctl restart mysqld;
   ```

3. **全局变量(GLOBAL)**: 全局变量针对于所有的会话

4. **会话变量(SESSION)**: 会话变量针对于单个会话，在另外一个会话窗口就不生效了

#### 4.2.3.2 用户定义变量

1. 赋值时，可以使用 = ，也可以使用 :=

   （因为MySQL中没有==，=既可以是赋值也可以是判断是否相等，为作区分推荐使用 := ）

2. 用户自定义变量不需要声明，用一个@开头表示（两个@@是系统变量）

3. 用户自定义变量的作用域是当前连接。在另一个会话就无法使用

4. 用户自定义变量不需要对其进行声明和初始化，只不过获取到的值为null，不会报错。

5. 赋初值的同时隐式指定了变量类型。

```mysql
-- 赋值方式1 
set @myname = 'itcast';
set @myage := 10;
set @mygender := '男',@myhobby := 'java';
-- 赋值方式2
select @mycolor := 'red';
-- 赋值方式3 把查询到的结果赋值给变量
select count(*) into @mycount from tb_user;
```

```mysql
-- 使用
select @myname,@myage,@mygender,@myhobby;
select @mycolor , @mycount;
```

#### 4.2.3.3 局部变量

1. **局部变量**是根据需要定义的在局部生效的变量，可用作<u>存储过程内的局部变量和输入参数</u>。
2. **作用范围**是在其内声明的 **Begin ... End** 块。
3. 访问之前，需要 **DECLARE** 声明

- 声明

```mysql
-- 变量类型就是数据库字段类型：INT、BIGINT、CHAR、VARCHAR、DATE、TIME等
DECLARE 变量名 变量类型 [default 默认值 ] ;
```

- 赋值

```mysql
SET 变量名 = 值 ;

SET 变量名 := 值 ;

SELECT 字段名 INTO 变量名 FROM 表名 ... ;
```

- 演示示例

```mysql
-- 声明局部变量 - declare
-- 赋值
create procedure p2()
begin
   declare stu_count int default 0;
   select count(*) into stu_count from student;
   select stu_count;
end;

call p2();
```

### 4.2.4 if 条件语句

用于存储过程中

```mysql
IF 条件1 THEN
...
ELSEIF 条件2 THEN	-- 可选
...
ELSE	-- 可选
...
END IF;
```

score >= 85分，等级为优秀
score >= 60分 且 score < 85分，等级为及格
score < 60分，等级为不及格

```mysql
create procedure p3()
begin

   declare score int default 58;

   declare result varchar(10);

   if score >= 85 then
      set result := '优秀';
   elseif score >= 60 then
      set result := '及格';
   else
      set result := '不及格';
   end if;

   select result;
end;

call p3();
```

### 4.2.5 参数

- 介绍

  1. 在存储过程的形参列表中定义要输入的变量和要输出的变量。

  2. 在调用时，输入变量传入指定值，输出变量用用户自定义变量来接收

  3. 对于inout类型参数的使用：

     ```mysql
     set @score=78;
     call p(@score);
     select @score;
     ```

| 类型  |                  含义                  | 备注 |
| :---: | :------------------------------------: | :--: |
|  IN   | 该参数作为输入，也就是需要调用时传入值 | 默认 |
|  OUT  |  该参数作为输出，也就是可以作为返回值  |      |
| INOUT | 既可以作为输入参数，也可以作为输出参数 |      |

```mysql
CREATE PROCEDURE 存储过程名称 ([IN/ OUT/ INOUT 参数名 参数类型])
BEGIN
	-- SQL语句
END;
```

- 案例

根据传入参数score，判定当前分数对应的分数等级，并返回
score >= 85分，等级为优秀
score >= 60分 且 score < 85分，等级为及格
score < 60分，等级为不及格

```mysql
create procedure p4(in score int, out result varchar(10))
begin
   if score >= 85 then
      set result := '优秀';
   elseif score >= 60 then
      set result := '及格';
   else
      set result := '不及格';
   end if;
end;

-- 定义用户变量 @result来接收返回的数据, 用户变量可以不用声明
call p4(18, @result);
select @result;
```

### 4.2.6 case 条件语句

用于存储过程中

- 语法1

```mysql
CASE case_value
	WHEN when_value1 THEN statement_list1
	[WHEN when_value2 THEN statement_list 2] ...
	[ ELSE statement_list ]
END CASE;
```

- 语法2

```mysql
CASE
	WHEN search_condition1 THEN statement_list1
	[WHEN search_condition2 THEN statement_list2] ...
	[ELSE statement_list]
END CASE;
```

- 案例

```mysql
create
    definer = root@localhost procedure p(IN month int)
begin
    declare result varchar(10);
    case
        when month >=1 and month <=3 then
            set result := '第一季度';
        when month >=4 and month <=6 then
            set result := '第二季度';
        when month >=7 and month <=9 then
            set result := '第三季度';
        when month >=10 and month <=12 then
            set result := '第四季度';
        else
            set result := '非法参数';
    end case;
    select concat('您输入的月份为',month,'，所属的季度为：',result);
end;
```

### 4.2.7 while 循环语句

while 是有条件的循环控制语句。满足条件后再执行循环体中的SQL语句，具体语法为

```mysql
# 先判定条件，如果为true，则执行逻辑，否则不执行
WHILE 条件 DO
	SQL 逻辑...
END WHILE;
```

- 案例

```mysql
-- 从1累加到n
create procedure p(in n int)
begin
	declare total int default 0;
	while n>0 do
		set total := total + n;
		set n := n - 1;
	end while;
	select total;
end;

call p(10);
```

### 4.2.8 repeat 循环语句

有条件的循环控制语句，当**满足条件时退出循环**，语法为：

```mysql
#先执行一次逻辑，然后判定逻辑是否满足，如果满足，则退出。如果不满足，则继续下一次循环
REPEAT
	SQL 逻辑...
	UNTIL 条件
END REPEAT;
```

- 案例

```mysql
-- 从1累加到n
create procedure p(in n int)
begin
	declare total int default 0;
	repeat
		set total := total + n;
		set n := n - 1;
	until n<=0
	end repeat;
	select total;
end;

call p(10);
```

### 4.2.9 loop 循环语句

Loop 实现简单的循环，如果不在sat逻辑中增加退出循环的条件，可以用其来实现简单的死循环（**while true**）。Loop 可以配合一下两个语句使用来指定退出循环的条件：

1. **LEAVE**：配合循环使用，**退出循环****（break）**。
2. **ITERATE**：必须用在循环中，作用是跳过当前循环剩下的语句，**直接进入下一次循环****（continue）**。

```mysql
[begin_label:] LOOP
	SQL逻辑...
END LOOP[end_label];
```

```mysql
LEAVE label;-- 退出指定标记的循环体
ITERATE label; -- 直接进入下一次循环
```

- 示例

```mysql
-- 从1累加到n
create procedure p9(in n int)
begin
	declare total int default 0;
	sum:loop
		if n<=0 then
			leave sum;
        end if;
		set total := total + n;
		set n := n - 1;
	end loop sum;
	select total;
end;
```

```mysql
-- 累加从1到n的所有偶数
create procedure p9(in n int)
begin
	declare total int default 0;
	sum:loop
		if n<=0 then
			leave sum;
        if n%2=1 then 
        	set n := n - 1;
        	iterate sum;
        end if;
        set total := total + n;
		set n := n - 1;
	end loop sum;
	select total;
end;
```

### 4.2.10 游标 Cursor

游标（CURSOR）是用来存储查询结果集的数据类型，在存储过程和函数中可以使用游标对结果集进行循环的处理。如在存储过程中，如果要把查询的结果保存起来，用变量是无法实现的，这时候就需要用到游标。

英 /*ˈkɜːsə(r)*  (计算机荧光屏上的)光标，游标

游标的使用包括游标的声明、OPEN、FETCH 和 CLOSE，其语法分别如下。

- 声明游标

````mysql
DECLARE 游标名称 CURSOR FOR 查询语句；
-- 注意应该先声明局部变量，再声明游标
````

- 打开游标

```mysql
OPEN 游标名称;
```

- 获取游标记录

```mysql
FETCH 游标名称 INTO 变量[,变量];
```

- 关闭游标

```mysql
CLOSE 游标名称;
```

- 示例

````mysql
-- 根据传入的参数uage，来查询用户表tb_user中，所有的用户年龄小于等于uage的用户姓名(name)和专业(profession)，并将用户的姓名和专业插入到所创建的一张新表(id,name,profession)中。
create procedure p(in uage int)
begin
	-- 定义遍历游标时所需的临时变量
    declare uname varchar(100);
    declare upro varchat(100);
	-- 声明游标，存储查询结果集
	declare u_cursor cursor for select name,profession from tb_user where age <= uage;
	-- 创建表结构
	drop table if exist tb_user_pro;
	create table if not exist tb_user_pro(
    	id int primary key auto_increment,
        name varchar(100),
        profession varchar(100)
    );
    -- 开启游标
    open u_cursor;

    -- 获取游标中的记录
    while true do
    	fetch u_cursor into uname,upro;
    	insert into tb_user_pro values(null,uname,upro);
    end while;
    -- 关闭游标
    close u_cursor;
end;
````

以上代码存在问题，while循环是个死循环，即不知道什么时候结束循环，在下面的章节将进行分析

### 4.2.11 条件处理程序 Handler

**条件处理程序（Handler）**可以用来定义在流程控制结构执行过程中遇到问题时相应的处理步骤，**相当于Java中的异常处理**。具体语法为:

````mysql
-- 定义一个条件处理程序。当出现条件condition_value时执行handler_action操作，并执行 statement SQL语句
DECLARE handler_action HANDLER FOR condition_value [, condition_value]... statement ;
````

```mysql
-- 执行动作 handler_action
CONTINUE:继续执行当前程序
EXIT:终止执行当前程序
-- 执行条件 condition_value
SQLSTATE sqlstate_value:状态码，如02000表示fetch时没有数据
SQLWARNING:所有以01开头的SQLSTATE代码的简写
NOT FOUND:所有以02开头的SQLSTATE代码的简写(一般是指没有找到数据)
SQLEXCEPTION:所有没有被SQLWARNING 或 NOT FOUND捕获的SQLSTATE代码的简写
-- 可参考MySQL关于状态码的官方文档
https://dev.mysql.com/doc/mysql-errors/8.0/en/server-error-reference.html
```

示例：

可以在上面的代码的基础上增加声明一个条件处理程序

````mysql 
declare exit handler for SQLSTATE '02000' close u_cursor;
declare exit handler for NOT FOUND close u-cursor;#等效
````

## 4.3 存储函数

存储函数是有返回值的存储过程，存储函数的参数只能是IN类型的。具体语法如下:

```mysql
CREATE FUNCTION 存储函数名称(参数列表])
RETURNS type [characteristic ...]
BEGIN
	--SQL语句
	RETURN ...;
END;
```

1. 关于characteristic的说明:

​		characteristic是关于存储函数的特性说明，在8.0后由于binary logging默认开启，需要设置characteristic，从下面几个中选填即可。

- DETERMINISTIC：相同的输入参数总是产生相同的结果

- NO SQL：不包含SQL语句。
- READS SQLDATA：包含读取数据的语句，但不包含写入数据的语句。

2. 示例

````mysql
create function fun1(n int)
returns int deterministic
begin
	declare total int default 0;
	while n>0 do
		set total := total +n ;
		set n := n - 1;
	end while;
	return total;
end;
````

说明：存储函数必须有返回值，而且存储函数能做的存储过程都可以做，所以存储函数用的较少。

## 4.4 触发器

### 4.4.1 触发器简介

​		触发器是与表有关的数据库对象，指<u>在 insert/update/delete 之前或之后，触发并执行触发器中定义的 SQL 语句集合</u>。触发器的这种特性可以协助应用在数据库端**确保数据的完整性**，**日志记录**，**数据校验**等操作。

- 使用别名**OLD**和**NEW**来引用触发器中发生变化的记录内容，这与其他的数据库是相似的。现在触发器还只支持**行级触发**，不支持**语句级触发**。

  - 行级触发：一个语句（如update）影响了多少行数据就触发多少次

  - 语句级触发：一个语句只触发一次

|   触发器类型    |                      NEW 和OLD                       |
| :-------------: | :--------------------------------------------------: |
| INSERT 型触发器 |            NEW表示将要或者已经新增的数据             |
| UPDATE型触发器  | OLD表示修改之前的数据，NEW表示将要或已经修改后的数据 |
| DELETE型触发器  |            OLD表示将要或者已经删除的数据             |

## 4.4.2 触发器的使用语法

- 创建

```mysql
CREATE TRIGGER trigger_name
	BEFORE/ AFTER INSERT/ UPDATE/ DELETE -- 设置执行前触发还是执行后触发
	ON tbl_name -- 哪个表
	FOR EACH ROW -- 行级触发器
BEGIN
	trigger_stmt;
END;
```

- 查看

```mysql
SHOW TRIGGERS;
```

- 删除

```mysql
DROP TRIGSER [schema_name.]trigger_name; 
-- 删除指定数据库的触发器，如果没有指定schema_name，默认为当前数据库。
```

- 案例

通过触发器记录tb_user表的数据变更日志，将变更日志插入到日志表user_logs中，包含增加,修改，删除;

```mysql
create table user_logs(
	id int(11) not null auto_increment,
	operation varchar(20) not null comment '操作类型,insert/update/delete',
    operate_time datetime not null comment '操作时间',
	operate_id int(11) not null comment '操作的ID',
    operate_params varchar(500) comment '操作参数',
    primary key('id')
)engine=innodb default charset=utf8;
```

```mysql
-- 插入数据触发器
create trigger tb_user_insert_trigger
	after insert on tb_user for each row
begin
	insert into user_logs(id,operation,operation_time,operation_id,operation_params) 
	values(null,'insert',now(),NEW.id,concat('插入的数据内容为：id= ',NEW.id,', name=',NEW.name,',phone=',NEW.phone,',email=',NEW.email,',profession=',NEW.profession))
end;
```

```mysql
-- 查看当前表的触发器的信息
show trigger;
-- 删除指定的触发器
drop tb_user_insert_trigger;
```

```mysql 
-- 修改数据触发器
create trigger tb_user_update_trigger
	after update on tb_user for each row
begin
	insert into user_logs(id,operation,operation_time,operation_id,operation_params) 
	values(null,'update',now(),NEW.id,concat(
        '更新之前的数据内容为：id=',OLD.id,',name=',OLD.name,',phone=',OLD.phone,',email=',OLD.email,',profession=',OLD.profession,
        '|更新之后的数据内容为：id= ',NEW.id,',name=',NEW.name,',phone=',NEW.phone,',email=',NEW.email,',profession=',NEW.profession))
end;
```

```mysql
-- 删除数据的触发器
create trigger tb_user_delete_trigger
	after delete on tb_user for each row
begin
	insert into user_logs(id,operation,operation_time,operation_id,operation_params) 
	values(null,'delete',now(),NEW.id,concat(
        '删除的数据内容为：id=',OLD.id,',name=',OLD.name,',phone=',OLD.phone,',email=',OLD.email,',profession=',OLD.profession)
end;
```

# 5 锁

## 5.1 概述

​		**锁是计算机协调多个进程或线程并发访问某一资源的机制。**在数据库中，除传统的计算资源（CPU、RAM、I/0)的争用以外，数据也是一种供许多用户共享的资源。如何保证数据并发访问的一致性、有效性是所有数据库必须解决的一个问题，锁冲突也是影响数据库并发访问性能的一个重要因素。从这个角度来说，锁对数据库而言显得尤其重要，也更加复杂。

- MySQL中的锁，按照**锁的粒度**分，分为以下三类：

全局锁：锁定数据库中的所有表

表级锁：每次操作锁住整张表

行级锁：每次操作锁住对应的行数据。

## 5.2 全局锁（手动加锁）

### 5.2.1 介绍

全局锁就是对整个数据库实例加锁，加锁后整个实例就处于**只读**状态。DML、DDL语句的事务提交语句都将**被阻塞**。

典型的使用场景是做**全库的逻辑备份**，对所有的表进行锁定，从而获取一致性视图和表格。

### 5.2.2 语法

- 加全局锁

```mysql
flush tables with read lock ;
```

- 数据备份

```mysql
mysqldump -uroot –p1234 数据库名 > 备份文件名.sql
```

mysqldump不是sql语句，不在MySQL的对话框执行，直接在windows的命令行执行

- 释放锁

```mysql
unlock tables ;
```

- **在InnoDB引擎中，在备份时加上参数 --single-transaction 参数来完成==不加锁==的一致性数据备份（快照的形式）**

```mysql
mysqldump --single-transaction -uroot –p123456 itcast > itcast.sql
```

### 5.2.3 全局锁的特点

数据库中加全局锁，是一个比较重的操作，存在以下问题:

1. 如果在**主库**上备份，那么在备份期间都不能执行更新，业务基本上就得停摆。

2. 如果在**从库**上备份，那么在备份期间从库不能执行主库同步过来的二进制日志（binary logging)，会导致**主从延迟**。

## 5.3 表级锁

表级锁，每次操作锁住整张表

锁的粒度大，发生锁冲突的概率最高，并发度最低。

应用在MyISAM、InnoDB、BDB等存储引擎中。

- 对于表级锁，主要分为以下三类：

1. 表锁
2. 元数据锁（meta data lock，MDL）
3. 意向锁

### 5.3.2 表锁（手动加锁）

对于表锁，分为两类：

表共享**读锁**（read lock），简称读锁或共享锁。

表独占**写锁**（write lock），简称写锁或排它锁。

语法：

```mysql
-- 加锁：
lock tables 表名 read/write
```

```mysql
-- 释放锁：(客户端断开自动释放锁)
unlock tables
```

特点

A. 读锁

![](G:\JavaLearning2023\04_数据库\images\QQ截图20230911162837.png)

B. 写锁

![](G:\JavaLearning2023\04_数据库\images\QQ截图20230911163429.png)

**结论: 读锁不会阻塞其他客户端的读，但是会阻塞写。写锁既会阻塞其他客户端的读，又会阻塞其他客户端的写。**

### 5.3.3 元数据锁（自动加锁）

meta data lock , 元数据锁，简写MDL。

1. MDL加锁过程是**系统自动控制**，无需显式使用，在访问一张表的时候会自动加上。
2. MDL锁的主要作用是维护**元数据**（主要就是**表的结构**）的数据一致性，某一张表涉及到未提交的事务时，是不能够修改这张表的表结构的。==为了避免DML与DDL冲突，保证读写的正确性。==
3. MySQL5.5之后引入了MDL，当对一张表进行**增删改查**的时候，会自动加MDL**读锁**(共享)，当对**表结构进行变更**操作的时候，加MDL**写锁**(排他)

常见的SQL操作时，所添加的元数据锁：

| 对应SQL                                         | 锁类型                                  | 说明                                             |
| ----------------------------------------------- | --------------------------------------- | ------------------------------------------------ |
| lock tables xxx read / write                    | SHARED_READ_ONLY / SHARED_NO_READ_WRITE |                                                  |
| select . select ... lock in share mode          | SHARED_READ                             | 与SHARED_READ、SHARED_wRITE兼容，与EXCLUSIVE互斥 |
| insert . update、delete、 select ... for update | SHARED_WRITE                            | 与SHARED_READ、SHARED_WRITE兼容，与EXCLUSIVE互斥 |
| alter table ...                                 | EXCLUSIVE                               | 与其他的MDL都互斥                                |

查看当前表的元数据锁

```mysql
select object_ type,object_schema,object_name,lock_type.lock_duration from performance_schema.metadata_locks ;
```

 

### 5.3.4 意向锁（自动加锁）

> 使用背景：
>
> 当对表内的某行数据按主键查找更新时，会自动加行锁，这时如果另一个客户端要加表锁时，为防止冲突，就需要逐行检查是否有行锁。

​		为避免数据操作语言DML在执行时，加的**行锁与表锁的冲突**（如在update一条语句时，会对行数据加行锁），在InnoDB中引入了意向锁，表锁不用检查每行数据是否加锁，使用意向锁来减少表锁的检查。

1. **意向共享锁(IS)**: 由==**select ... lock in share mode**==添加 。与**表锁共享锁(read)**<u>兼容</u>，与**表锁排他锁(write)**<u>互斥</u>。
2. **意向排他锁(IX)**: 由insert、update、delete、==**SELECT ... FOR UPDATE**==添加 。与**表锁共享锁**(read)及**排他锁**(write)都<u>互斥</u>，**意向锁之间不会互斥**。

一旦事务提交了，意向共享锁、意向排他锁，都会自动释放。

可以通过以下SQL，查看意向锁及行锁的加锁情况：

```mysql
select object_schema,object_name,index_name,lock_type,lock_mode,lock_data from
performance_schema.data_locks;
```

## 5.4 行级锁（自动加锁）

### 5.4.1 介绍

> 行级锁每次操作锁对应的行数据。锁定粒度最小，发生锁冲突的概率最低，并发度最高。主要应用在InnoDB存储引擎中（MyISAM不支持行锁）。

InnoDB的数据是基于**索引**的，行锁是**对索引上的索引项加锁**来实现的，而不是对记录加的锁。

对于行级锁，主要分为以下三类：

1. 行锁（Record Lock）：锁定单个行记录的锁，防止其他事务对此行进行update和delete。在RC、RR隔离级别下都支持。
2. 间隙锁（Gap Lock）：锁定索引记录间隙（不含该记录），确保索引记录间隙不变，防止其他事务在这个间隙进行insert，产生幻读

3. 临键锁（Next-Key Lock）：行锁和间隙锁组合，同时锁住数据，并锁住数据前面的间隙Gap


### 5.4.2 行锁

**共享锁**（**S**）：允许一个事务去读一行，阻止其他事务获得相同数据集的排它锁。即共享锁与共享锁之间兼容，与排它锁互斥。

**排他锁**（**X**）：允许获取排他锁的事务更新数据，阻止其他事务获得相同数据集的共享锁和排他锁。

常见的SQL语句，在执行时，所加的行锁如下：

| SQL                          | 行锁类型   | 说明               |
| ---------------------------- | ---------- | ------------------ |
| INSERT...                    | 排他锁     | 自动加锁           |
| UPDATE ...                   | 排他锁     | 自动加锁           |
| DELETE. ...                  | 排他锁     | 自动加锁           |
| SELECT（正常)                | 不加任何锁 |                    |
| SELECT ...LOCK IN SHARE MODE | 共享锁     | LOCK IN SHARE MODE |
| SELECT ... FOR UPDATE        | 排他锁     | FOR UPDATE         |

即执行SELECT ...LOCK IN SHARE MODE后会在该行加（行级）共享锁，在该表加（表级）意向共享锁；执行SELECT ... FOR UPDATE后会在该行加（行级）排他锁，在该表加（表级）意向排他锁。



默认情况下，InnoDB在REPEATABLE READ事务隔离级别运行，InnoDB使用next-key锁进行搜索和索引扫描，以防止幻读。

1. 针对唯一索引进行检索时，对已存在的记录进行等值匹配时，将会自动优化为行锁。

2. InnoDB的行锁是针对于索引加的锁，不通过索引条件检索数据，那么InnoDB将对表中的所有记录加锁，此时就会升级为表锁。

### 5.4.3 间隙锁

​		当我们用条件检索数据，并请求共享或排他锁时，InnoDB不仅会对符合条件的id值为101的记录加锁，也会对id大于101（这些记录并不存在）的“间隙”加锁。**使用间隙锁锁住的是一个区间**。

​		这个时候如果你插入empid等于102的数据的，如果那边事物还没有提交，那你就会处于等待状态，无法插入数据。

```mysql
 SELECT * FROM emp WHERE empid > 100 FOR UPDATE
```

```mysql
update stu set age = 10 where id = 5;
-- 原表中只有id=3和id=8，在执行该语句后，会给id=3~8之间的间隙加间隙（排他）锁。
-- 此时无法在其他客户端在id=4~7之内插入数据
insert into stu values(7,'Rudy',10); # 会陷入阻塞状态
```

### 5.4.4 临键锁（next-key locks）

**Next-key锁是记录锁和间隙锁的组合，它指的是加在某条记录以及这条记录前面间隙上的锁**。默认情况下，InnoDB在REPEATABLE READ事务隔离级别运行，InnoDB使用next-key锁进行搜索和索引扫描，以防止幻读。

临键锁可以理解为一种特殊的**间隙锁**。通过**临建锁**可以解决`幻读`的问题。 每个数据行上的**非唯一索引列**上都会存在一把**临键锁**，当某个事务持有该数据行的**临键锁**时，会锁住一段**左开右闭区间**的数据。需要强调的一点是，InnoDB 中**行级锁**是基于索引实现的，**临键锁**只与**非唯一索引列**有关，在`唯一索引列（包括主键列）上不存在临键锁`。

假设有如下表：

InnoDB，RR隔离级别：id主键, age 普通索引

| id   | age  | name     |
| ---- | ---- | -------- |
| 1    | 10   | zhangsan |
| 3    | 24   | lisi     |
| 5    | 32   | wangwu   |
| 7    | 45   | zhaoliu  |

该表中 age 列潜在的临键锁有：
(-∞, 10],
(10, 24],
(24, 32],
(32, 45],
(45, +∞],

在事务 A 中执行如下命令：

```mysql
-- 根据非唯一索引列 UPDATE 某条记录 
UPDATE table SET name = Vladimir WHERE age = 24; 
-- 或根据非唯一索引列 锁住某条记录 
SELECT * FROM table WHERE age = 24 FOR UPDATE; 
```

不管执行了上述 SQL 中的哪一句，之后如果在事务 B 中执行以下命令，则该命令会被阻塞：

```mysql
INSERT INTO table VALUES(100, 26, 'tianqi'); 
```

很明显，事务 A 在对 age 为 24 的列进行 UPDATE 操作的同时，也获取了 (24, 32] 这个区间内的临键锁。



这里对 **记录锁**、**间隙锁**、**临键锁** 做一个总结

- **InnoDB** 中的**行锁**的实现依赖于**索引**，一旦某个加锁操作没有使用到索引，那么该锁就会退化为`表锁`。
- **记录锁**存在于包括**主键索引**在内的**唯一索引**中，锁定单条索引记录。
- **间隙锁**存在于**非唯一索引**中，锁定**开区间**范围内的一段间隔，它是基于**临键锁**实现的。
- **临键锁**存在于**非唯一索引**中，该类型的每条记录的索引上都存在这种锁，它是一种特殊的**间隙锁**，锁定一段**左开右闭**的索引区间。

链接：https://www.jianshu.com/p/478bc84a7721
来源：简书





1.索引上的等值查询(唯一索引)，给不存在的记录加锁时,优化为间隙锁。

2.索引上的等值查询(普通索引)，向右遍历时最后一个值不满足查询需求时，next-key lock退化为间隙锁。

3.索引上的范围查询(唯一索引)--会访问到不满足条件的第一个值为止。

注意:间隙锁唯一目的是防止其他事务插入间隙。间隙锁可以共存，一个事务采用的间隙锁不会阻止另一个事务在同一间隙上采用间隙锁。

————————————————
版权声明：本文为CSDN博主「天外流星LX」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/weixin_50523986/article/details/131192742

==注意：关于临键锁和间隙锁情况较复杂，上面的内容也不尽正确和完善，可以参考[深入分析MySQL行锁加锁规则_supremum pseudo-record_qq_39745899的博客-CSDN博客](https://blog.csdn.net/qq_39745899/article/details/127618751)==

该文3.2实测是加临键锁而不是间隙锁。

# 6 InnoDB 存储引擎

## 6.1 逻辑结构

![](G:\JavaLearning2023\04_数据库\images\QQ截图20230912200754.png)

1. **表空间**：xxx.ibd

2. **段**：分为数据段，索引段，回滚段。

   InnoDB是索引组织表，数据段就是B+树的叶子节点， 索引段即为B+树的非叶子节点。段用来管理多个Extent（区）

3. **区**：表空间的单元结构，每个区的大小为**1M**
   默认情况下， InnoDB存储引擎页大小为16K， 即一个区中一共有64个连续的页

4. **页**：InnoDB 存储引擎磁盘管理的最小单元，每个页的大小默认为 **16KB**，InnoDB 存储引擎每次从磁盘申请 4-5 个区

5. **行**：InnoDB 存储引擎数据是按行进行存放的，默认有两个隐藏字段
   Trx_id：每次对某条记录进行改动时，都会把对应的事务id赋值给trx_id隐藏列
   Roll_pointer： 把旧的版本写入到undo日志中，然后这个隐藏列就相当于一个指针，可以通过它来找到该记录修改前的信息

## 6.2 InnoDB的架构

![](G:\JavaLearning2023\04_数据库\images\67cbe772547c4401bae979e039828c20.png)

### 6.2.1 内存结构

![](G:\JavaLearning2023\04_数据库\images\QQ截图20230912222931.png)

1. **Buffer pool**：缓冲池是主内存中的一个区域，里面可以缓存磁盘上经常操作的真实数据，在执行增删改查操作时，先操作缓冲池中的数据（若缓冲池没有数据，则从磁盘加载并缓存），然后再以一定频率刷新到磁盘，从而减少磁盘IO，加快处理速度。

- 缓冲池以Page页为单位，底层采用链表数据结构管理Page。根据状态，将Page分为三种类型：

  - free page：空闲page，未被使用。

  - clean page：被使用page，数据没有被修改过。

  - dirty page：脏页，被使用page，数据被修改过，数据与磁盘的数据产生了不一致

2. **Change Buffer**：更改缓冲区（针对于**非唯一二级索引页**），在执行DML语句时，**如果这些数据Page没有在Buffer Pool中，不会会直接操作磁盘，而会将数据变更在更新缓存区Change Buffer**中，在未来数据被读取时，在将数据合并恢复到Buffer Pool中，再将合并后的数据刷新到磁盘中。

- Change Buffer 的意义是？与聚集索引不同，二级索引通常是非唯一的，并且以相对随机的顺序插入二级索引。同样，删除和更新可能会影响索引树中不相邻的二级索引页，如果每一次都操作磁盘，**会造成大量的随机的磁盘IO**。有了Change Buffer之后，我们可以在缓冲池中进行合并处理。减少磁盘IO。

3. **Adaptive Hash Index**：自适应hash索引，用于优化对Buffer Pool数据的查询。InnoDB存储引擎会监控表上各索引页的查询，如果观察到hash索引可以提升速度，则建立hash索引，称之为自适应hash索引。（**主要就是结合索引和Hash索引的优点**）

   - 自适应哈希索引，无序人工干预，是系统根据情况自动完成

   - 参数：adaptive_hash_index   自适应hash索引是否开启

   ```mysql
   show variables like '%adaptive_hash_index%';
   ```

4. **Log Buffer**：日志缓冲区，用来保存要写入到磁盘中的log日志数据（redo log、undo log），默认大小为16MB，日志缓冲区的日志会定期刷新到磁盘汇总。如果需要更新、插入或删除许多行的事务，增加日志缓冲区的大小可以节省磁盘I/O

- 参数：

  - innodb_log_buffer_size:缓冲区大小

  - innodb_flush_log_at_trx_commit：日志刷新到磁盘时机

    - 1：日志在每次事务提交时写入并刷新到磁盘

    - 0：每秒将日志写入并刷新到磁盘一次

    - 2：日志在每次事务提交后写入，并每秒刷新到磁盘一次

```mysql
show variables  like '%innodb_log_buffer_size'
show variables like '%innodb_flush_log_at_trx_commit'
```

### 6.2.2 磁盘结构

1. System Tablespace：系统表空间是更改缓冲区的存储区域。如果表是在系统表空间而不是每个表文件或通用表空间中创建的，它也可能包含表和索引数据（在MySQL5.X版本中还包含InnoDB数据字典、undolog等）

   参数：innodb_data_file_path

```mysql
show variables like '%innodb_data_file_path'
```

2. File-Per-Table Tablespaces:每个表的文件表空间包含单个InnoDb表的数据和索引，并存储在文件系统上的的那个数据文件中。 .ibd文件

   参数：innodb_file_par_table

   ```mysql
   show variables like '%innodb_file_per_table'
   ```

3. General Tablespaces：通用表空间，需要通过create tablespace 语法创建通用表空间，在创建表的时候，可以指定该表空间

```mysql
create tablespace ts_test add datafile  'wintertest.ibd' engine = innodn;
create table  a (id int primary key auto_increment,name varchar(20) not null
) engine = innodb tablespace ts_test;
```

4. Undo Tablespaces：撤销表空间，MySQL实例在初始化时会自动创建两个默认undo表空间（初始大小16M），用于存储undo log 日志 undo_001, undo_002

5. Temporary Tablespaces：InnoDB使用会话临时表空间和全局临时表空间。存储用户创建的临时表等数据

6. Doublewrite Buffer Files：双写缓冲区，innoDB引擎将数据页从Buffer Pool刷新到磁盘前，先将数据页写入双写缓冲区文件中，便于系统异常时恢复数据。

   \#ib_16384_0.dblwr,#ib_16384_1.dblwr

7. Redo Log:重做日志，是用来实现事务的持久性。该日志文件由两部分组成：重写缓冲（redo log buffer）以及重做日志文件（redo log），前者是在内存中，后者在磁盘中。当时事务提交之后会把所有修改信息都会存到该日志中，用于刷新脏页到磁盘时，发生错误时，进行数据恢复使用。

   以循环方式重写日志文件，涉及两个文件：ib_logfile0 , ib_logfile1

### 6.2.3 后台线程

将内存数据刷新到磁盘所涉及的线程

1. Master Thread

   核心后台线程，负责调度其他线程，还负责将缓冲池中的数据异步刷新到磁盘中，保持数据的一致性，还包括脏页的刷新、合并插入缓存、undo页的回收。

2. IO Thread

   在InnoDB存储引擎中大量使用AIO来处理IO请求，这样可以极大地提高数据库的性能，而IO Thread主要负责这些IO请求的回调。

   线程类型

   默认				   			个数	 	职责

   Read thread				    4		   负责读操作

   Write thread	   			4		   负责写操作

   Log thread		  			1		   负责将日志缓冲区刷新到磁盘

   Insert buffer thread  	1  		 负责将写缓冲区内容刷新到磁盘

3. Purge Thread

   主要用于回收事务已经提交的undo log ，在事务提交之后，undo log可能不用了，就用它来回收。

4. Page Cleaner Thread

   协助Master Thread 刷新脏页到磁盘的线程，它可以减轻Master Thread的工作压力，减少阻塞

## 6.3 事务的原理

**Redo log**  **<font color='red'>事务的持久性</font>**

重做日志，记录的是事务提交时数据页的物理修改，是用来里实现事务的持久性。

**<font color='red'>数据提交后会存在内存的Buffer pool的脏页中，后续一起刷进磁盘中。在此之前会先在磁盘的redo log file中记录日志，如果在脏页刷入磁盘出错时，会起用日志进行数据恢复。</font>**

- 组成：**重做日志缓冲**（redo log buffer）及**重做日志文件**（redo log file），前者是在内存中，后者是在磁盘中。当事务提交之后会把所有修改信息都存到该日志文件中，用于刷新脏页到磁盘，发生错误时，进行数据恢复使用。
- 主要作用：`redo log`（重做日志）的设计主要是为了防止因**系统崩溃而导致的数据丢失**。
- 数据恢复的原理：

  1. 每次提交事务之前，必须将所有和当前事务相关的 **`Buffer Pool`中的脏页** 刷入磁盘，但是这个效率比较低，可能会影响主线程的效率，产生用户等待，降低响应速度，因为刷盘是`I/O`操作，同时一个事务的读写操作也不是顺序读写。

  2. 把当前事务中修改的数据内容在日志中记录下来，日志是**顺序写**，性能很高，其实mysql就是这么做的，这个日志被称为`redo log`。执行事务中，每执行一条语句，就可能有若干`redo`日志，并按产生的顺序写入磁盘，`redo`日志占用的空间非常小，当`redo log`空间满了以后又会从头开始以循环的方式进行覆盖式的写入。

- **为什么不直接把Buffer pool中数据刷入磁盘，要日志先行？**日志是磁盘顺序IO（追加）效率较高，而脏页刷入磁盘是随机磁盘IO，效率较低。

![](G:\JavaLearning2023\04_数据库\images\dd652e5676f244c5942be33debe55331.png)

![](G:\JavaLearning2023\04_数据库\images\de732281dffa48d487b9d57bc1a296ee.png)

**Undo log** **<font color='red'>事务的原子性</font>**

回滚日志，用于**记录数据被<font color='red'>修改前</font>的信息**，作用包含两个：提供回滚和MVCC（多版本并发控制）

undo log和redo log 记录**<font color='red'>物理</font>日志**不一样，它是**<font color='red'>逻辑</font>日志**，可以认为当delete一条记录时，undo log 中会记录一条对应的insert记录，反之亦然，当update一条记录时，它记录一条对应相反的update记录。当执行rollback时，乐园从undo log中的逻辑记录读取到响应的内容并进行回滚**<font color='red'>（执行undo log中的逻辑可以使得数据恢复到修改前的状态）</font>**。

**undo log 销毁**：undo log 在事务执行时产生，事务提交时，并不会立即删除undo log ，因为这些日志可能还用于MVCC

**undo log 存储**：undo log 采用段的方式进行管理和记录，存放在前面介绍的rollback segment回滚段中，内部包含1024个undo log segment

**由MVCC和锁实现数据的隔离性**

## 6.4 MVCC

### 6.4.1 基本概念

- **当前读**（LOCKING READ）

  读取的是记录的**最新版本**，读取时还要保证其他并发事务不能修改当前记录，会对读取的记录进行**<font color='red'>加锁</font>**。对于我们日常的操作，如：select ... lock in share mode(共享锁)，select ... for update 、update、insert、delete（排他锁）都是一种当前读

- **快照读**

  简单的select（不加锁）就是快照读，快照读读到的是记录数据的可见版本，有可能是历史数据，不加锁，是**非阻塞读**。

  - Read Committed：每次select，都生产一个快照读
  - Repatable Read：开启事务后第一个select语句才是快照读的地方。
  - Serializable：快照读会退化为当前读。

- **MVCC**

  全称 Multi-Version Concurrency Control，**<font color='red'>多版本并发控制</font>**。指维护一个数据的多个版本，使得读写操作没有冲突，快照读为MySQL实现MVCC提供了一个非阻塞读功能。MVCC的具体实现，还需要依赖与数据记录中的是**三个<font color='red'>隐式字段</font>、<font color='red'>undo log</font>日志、<font color='red'>readView</font>**。

- **隐藏字段**

| 隐藏字段    | 含义                                                         |
| ----------- | ------------------------------------------------------------ |
| DB_TRX_ID   | 最近修改事务ID，自增的全局变量。每次插入和修改该记录都会修改事务ID |
| DB_ROLL_PTR | 回滚指针，指向这条记录的上一个版本，用于配合undo log，指向上一个版本 |
| DB_ROW_ID   | 隐藏主键，如果表结构没有指定主键，将会生成该隐藏字段         |

- 如果有主键就没有该字段DB_ROW_ID

- 查看idb文件的方法：idb2sdi + 表空间文件名.idb	可以在columns中找到隐藏字段 
- 关于DB_TRX_ID，**每个事务开启时，都会分配到一个事务id，这个id是严格单调递增且唯一的**。如果一个【**事务**】对任意表做了【**增删改**】的操作，那么这个事务ID就会被赋给DB_TRX_ID。

### 6.4.2 Undo log 和 版本控制指针

两个隐藏指针 DB_TRX_ID 和 DB_ROLL_PTR 记录各记录的版本信息，未做日志 Undo log 在insert、update、delete的时候产生便于数据回滚的日志。

当insert的时候，事务版本ID为1，回滚指针**<font color='red'>没有指向任何版本</font>**。（insert）产生的 undo log 日志只在回滚时需要，在事务提交后，可被立即删除

![](G:\JavaLearning2023\04_数据库\images\QQ截图20230913131926.png)

其他客户端开启事务进行修改和删除操作时，要**<font color='red'>先</font>**记录 Undo log 日志。事务ID指针自增，事务回滚指针指向**前一个版本的日志信息**。

而 update、delete 的时候，产生的 undo log 日志不仅在回滚时需要，在快照时也需要，不会立即被删除**（当前还有相关联的活跃事务）**。

![](G:\JavaLearning2023\04_数据库\images\QQ截图20230913134010.png)

不同事务或相同事务对同一条记录进行修改，会导致该记录的undo log生成一条记录版本链条，**==链表的头部==**是**最<font color='red'>新</font>**的旧记录，**==链表的尾部==**是**<font color='red'>最早</font>**的记录。

注意：事务一旦提交，就无法回滚。

### 6.4.3 ReadView

ReadView（读视图）是**快照读**SQL执行时MVCC提供数据的依据，记录并维护系统当前活跃的事务id。

| 字段           | 含义                                                         |
| -------------- | ------------------------------------------------------------ |
| m_ids          | 当前活跃的事务ID记录 （未提交的事务）                        |
| min_trx_id     | 最小活跃事务ID                                               |
| max_trx_id     | 预分配事务ID，当前最大事务ID+1，系统中应该分配给下一个事务的id值 |
| creator_trx_id | ReadView创建者的事务ID                                       |

<font color='red'>**注意max_trx_id不是最大事务ID**</font>

1. **版本数据访问规则** （trx_id代表被访问版本的DB_TRX_ID）:**还未提交和未开启的事务的版本不可以被访问**

①trx_id == creator_trx_id ?可以访问该版本成立，说明数据是当前这个事务更改的；

②trx_id < readview中的min_trx_id值，表明生成该版本的事务在当前事务生成readview前已经提交，该版本可以被当前事务访问；

③trx_id >= readview中的max_trx_id值，表明生成该版本的事务在当前事务生成readview后才开启，该版本不可以被当前事务访问；

④min_trx_id < trx_id < max_trx_id之间，就需要判断trx_id属性值是不是在m_ids列表中？
如果在：说明创建readview时生成该版本的事务还是活跃的，该版本不可以被访问
如果不在：说明创建readview时生成该版本的事务已经被提交，该版本可以被访问；

2. **生成ReadView的时机**

不同的隔离级别，生成ReadView时机不同：

Read COMMITTED:在事务中每一次执行快照时生成 ReadView。

Repeatable READ：**<font color='red'>仅在事务中第一次执行快照读时生成ReadView，后续复用该ReadView</font>**。所以可重复读所有读取的数据都是第一次快照读时的readview

3. **示例**

RC:

![](G:\JavaLearning2023\04_数据库\images\QQ截图20230913145226.png)

RR：

![](G:\JavaLearning2023\04_数据库\images\QQ截图20230913151117.png)

# 7 数据库管理

## 7.1 系统数据库

MySQL自带系统数据库

| 数据库             | 含义                                                         |
| ------------------ | ------------------------------------------------------------ |
| mysql              | 存储MySQL服务器正常运行所需要的各种信息(时区、主从、用户，权限等) |
| information_schema | 提供了访问数据库元数据的各种表和视图，包含数据库、表、字段类型及访问权限等 |
| performance_schema | 为MysQL服务器运行时状态提供了一个底层监控功能，主要用于收集数据库服务器性能参数 |
| sys                | 包含了一系列方便DBA和开发人员利用performance_schema性能数据库进行性能调优和诊断的视图 |

## 7.2 常用工具

### 7.2.1 mysql

该mysql不是指mysql服务，而是指mysql的客户端工具。

- 语法:

​	mysql [options] [database]

- 选项:

​	-u --User=name	#指定用户名
​	-P --password[=name]	#指定密码
​	-h --host=name	#指定服务器IP或域名
​	-P --port=port	#指定连接端口
​	-e --execute=name	#执行SQL语句井源出

- -e选项可以在Mysql客户端执行saL语句，而不用连接到MysQL数据库再执行，对于一些批处理脚本，这种方式尤其方便。

- 示例:

```powershell
mysql -uroot -p123456 db01 -e "select * from stu";
```

### 7.2.2 mysqladmin

mysqladmin 是一个执行管理操作的客户端程序。可以用它来检查服务器的配置和当前状态、创建并删除数据库，可以用mysqladmin查看帮助文档选项

示例：

mysqladmin -uroot -p123456 drop 'test01';

mysqladmin -uroot -p123456 version;

### 7.2.3 mysqlbinlog

由于服务器生成的二进制日志文件以二进制格式保存，所以如果想要检查这些文本的文本格式，就会使用到mysqlbinlog日志管理工具。

![](G:\JavaLearning2023\04_数据库\images\QQ截图20230914140231.png)

### 7.2.4 mysqlshow 

mysqlshow客户端对象查找工具，用来很快地查找存在哪些数据库、数据库中的表、表中的列或者索引。

![](G:\JavaLearning2023\04_数据库\images\QQ截图20230914140509.png)

### 7.2.5 mysqldump

mysqldump客户端工具用来备份数据库或在不同数据库之间进行数据迁移。备份内容包含创建表，及插入表的saL语句。

![](G:\JavaLearning2023\04_数据库\images\QQ截图20230914140554.png)

### 7.2.6 source/mysqlimport

mysqlimport是客户端数据导入工具，用来导入mysqldump加-T参数后导出的文本文件。(不能执行.sql脚本)

![](G:\JavaLearning2023\04_数据库\images\QQ截图20230914140726.png)

如果需要导入sql文件,可以使用mysql中的source指令:

语法：

source /root/xxxxx.sql

# 数据类型

## 整型

| 类型名称      | 取值范围                                 | 大小    |
| ------------- | ---------------------------------------- | ------- |
| TINYINT       | -128~127                                 | 1个字节 |
| SMALLINT      | -32768~32767                             | 2个宇节 |
| MEDIUMINT     | -8388608~8388607                         | 3个字节 |
| INT (INTEGHR) | -2147483648~2147483647                   | 4个字节 |
| BIGINT        | -9223372036854775808~9223372036854775807 | 8个字节 |

无符号在数据类型后加 unsigned 关键字。

## 浮点型

| 类型名称            | 说明               | 存储需求   |
| ------------------- | ------------------ | ---------- |
| FLOAT               | 单精度浮点数       | 4 个字节   |
| DOUBLE              | 双精度浮点数       | 8 个字节   |
| DECIMAL (M, D)，DEC | 压缩的“严格”定点数 | M+2 个字节 |

## 日期和时间

| 类型名称  | 日期格式            | 日期范围                                          | 存储需求 |
| --------- | ------------------- | ------------------------------------------------- | -------- |
| YEAR      | YYYY                | 1901 ~ 2155                                       | 1 个字节 |
| TIME      | HH:MM:SS            | -838:59:59 ~ 838:59:59                            | 3 个字节 |
| DATE      | YYYY-MM-DD          | 1000-01-01 ~ 9999-12-3                            | 3 个字节 |
| DATETIME  | YYYY-MM-DD HH:MM:SS | 1000-01-01 00:00:00 ~ 9999-12-31 23:59:59         | 8 个字节 |
| TIMESTAMP | YYYY-MM-DD HH:MM:SS | 1980-01-01 00:00:01 UTC ~ 2040-01-19 03:14:07 UTC | 4 个字节 |

## 字符串

| 类型名称   | 说明                                         | 存储需求                                                   |
| ---------- | -------------------------------------------- | ---------------------------------------------------------- |
| CHAR(M)    | 固定长度非二进制字符串                       | M 字节，1<=M<=255                                          |
| VARCHAR(M) | 变长非二进制字符串                           | L+1字节，在此，L< = M和 1<=M<=255                          |
| TINYTEXT   | 非常小的非二进制字符串                       | L+1字节，在此，L<2^8                                       |
| TEXT       | 小的非二进制字符串                           | L+2字节，在此，L<2^16                                      |
| MEDIUMTEXT | 中等大小的非二进制字符串                     | L+3字节，在此，L<2^24                                      |
| LONGTEXT   | 大的非二进制字符串                           | L+4字节，在此，L<2^32                                      |
| ENUM       | 枚举类型，只能有一个枚举字符串值             | 1或2个字节，取决于枚举值的数目 (最大值为65535)             |
| SET        | 一个设置，字符串对象可以有零个或 多个SET成员 | 1、2、3、4或8个字节，取决于集合 成员的数量（最多64个成员） |

## 二进制类型

| 类型名称       | 说明                 | 存储需求               |
| -------------- | -------------------- | ---------------------- |
| BIT(M)         | 位字段类型           | 大约 (M+7)/8 字节      |
| BINARY(M)      | 固定长度二进制字符串 | M 字节                 |
| VARBINARY (M)  | 可变长度二进制字符串 | M+1 字节               |
| TINYBLOB (M)   | 非常小的BLOB         | L+1 字节，在此，L<2^8  |
| BLOB (M)       | 小 BLOB              | L+2 字节，在此，L<2^16 |
| MEDIUMBLOB (M) | 中等大小的BLOB       | L+3 字节，在此，L<2^24 |
| LONGBLOB (M)   | 非常大的BLOB         | L+4 字节，在此，L<2^32 |

# 权限一览表

> 具体权限的作用详见[官方文档](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html "官方文档")

GRANT 和 REVOKE 允许的静态权限

| Privilege                                                    | Grant Table Column           | Context                               |
| :----------------------------------------------------------- | :--------------------------- | :------------------------------------ |
| [`ALL [PRIVILEGES]`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_all) | Synonym for “all privileges” | Server administration                 |
| [`ALTER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_alter) | `Alter_priv`                 | Tables                                |
| [`ALTER ROUTINE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_alter-routine) | `Alter_routine_priv`         | Stored routines                       |
| [`CREATE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create) | `Create_priv`                | Databases, tables, or indexes         |
| [`CREATE ROLE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create-role) | `Create_role_priv`           | Server administration                 |
| [`CREATE ROUTINE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create-routine) | `Create_routine_priv`        | Stored routines                       |
| [`CREATE TABLESPACE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create-tablespace) | `Create_tablespace_priv`     | Server administration                 |
| [`CREATE TEMPORARY TABLES`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create-temporary-tables) | `Create_tmp_table_priv`      | Tables                                |
| [`CREATE USER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create-user) | `Create_user_priv`           | Server administration                 |
| [`CREATE VIEW`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_create-view) | `Create_view_priv`           | Views                                 |
| [`DELETE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_delete) | `Delete_priv`                | Tables                                |
| [`DROP`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_drop) | `Drop_priv`                  | Databases, tables, or views           |
| [`DROP ROLE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_drop-role) | `Drop_role_priv`             | Server administration                 |
| [`EVENT`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_event) | `Event_priv`                 | Databases                             |
| [`EXECUTE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_execute) | `Execute_priv`               | Stored routines                       |
| [`FILE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_file) | `File_priv`                  | File access on server host            |
| [`GRANT OPTION`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_grant-option) | `Grant_priv`                 | Databases, tables, or stored routines |
| [`INDEX`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_index) | `Index_priv`                 | Tables                                |
| [`INSERT`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_insert) | `Insert_priv`                | Tables or columns                     |
| [`LOCK TABLES`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_lock-tables) | `Lock_tables_priv`           | Databases                             |
| [`PROCESS`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_process) | `Process_priv`               | Server administration                 |
| [`PROXY`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_proxy) | See `proxies_priv` table     | Server administration                 |
| [`REFERENCES`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_references) | `References_priv`            | Databases or tables                   |
| [`RELOAD`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_reload) | `Reload_priv`                | Server administration                 |
| [`REPLICATION CLIENT`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_replication-client) | `Repl_client_priv`           | Server administration                 |
| [`REPLICATION SLAVE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_replication-slave) | `Repl_slave_priv`            | Server administration                 |
| [`SELECT`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_select) | `Select_priv`                | Tables or columns                     |
| [`SHOW DATABASES`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_show-databases) | `Show_db_priv`               | Server administration                 |
| [`SHOW VIEW`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_show-view) | `Show_view_priv`             | Views                                 |
| [`SHUTDOWN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_shutdown) | `Shutdown_priv`              | Server administration                 |
| [`SUPER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_super) | `Super_priv`                 | Server administration                 |
| [`TRIGGER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_trigger) | `Trigger_priv`               | Tables                                |
| [`UPDATE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_update) | `Update_priv`                | Tables or columns                     |
| [`USAGE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_usage) | Synonym for “no privileges”  | Server administration                 |

GRANT 和 REVOKE 允许的动态权限

| Privilege                                                    | Context                                           |
| :----------------------------------------------------------- | :------------------------------------------------ |
| [`APPLICATION_PASSWORD_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_application-password-admin) | Dual password administration                      |
| [`AUDIT_ABORT_EXEMPT`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_audit-abort-exempt) | Allow queries blocked by audit log filter         |
| [`AUDIT_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_audit-admin) | Audit log administration                          |
| [`AUTHENTICATION_POLICY_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_authentication-policy-admin) | Authentication administration                     |
| [`BACKUP_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_backup-admin) | Backup administration                             |
| [`BINLOG_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_binlog-admin) | Backup and Replication administration             |
| [`BINLOG_ENCRYPTION_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_binlog-encryption-admin) | Backup and Replication administration             |
| [`CLONE_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_clone-admin) | Clone administration                              |
| [`CONNECTION_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_connection-admin) | Server administration                             |
| [`ENCRYPTION_KEY_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_encryption-key-admin) | Server administration                             |
| [`FIREWALL_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_firewall-admin) | Firewall administration                           |
| [`FIREWALL_EXEMPT`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_firewall-exempt) | Firewall administration                           |
| [`FIREWALL_USER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_firewall-user) | Firewall administration                           |
| [`FLUSH_OPTIMIZER_COSTS`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_flush-optimizer-costs) | Server administration                             |
| [`FLUSH_STATUS`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_flush-status) | Server administration                             |
| [`FLUSH_TABLES`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_flush-tables) | Server administration                             |
| [`FLUSH_USER_RESOURCES`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_flush-user-resources) | Server administration                             |
| [`GROUP_REPLICATION_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_group-replication-admin) | Replication administration                        |
| [`GROUP_REPLICATION_STREAM`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_group-replication-stream) | Replication administration                        |
| [`INNODB_REDO_LOG_ARCHIVE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_innodb-redo-log-archive) | Redo log archiving administration                 |
| [`NDB_STORED_USER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_ndb-stored-user) | NDB Cluster                                       |
| [`PASSWORDLESS_USER_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_passwordless-user-admin) | Authentication administration                     |
| [`PERSIST_RO_VARIABLES_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_persist-ro-variables-admin) | Server administration                             |
| [`REPLICATION_APPLIER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_replication-applier) | `PRIVILEGE_CHECKS_USER` for a replication channel |
| [`REPLICATION_SLAVE_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_replication-slave-admin) | Replication administration                        |
| [`RESOURCE_GROUP_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_resource-group-admin) | Resource group administration                     |
| [`RESOURCE_GROUP_USER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_resource-group-user) | Resource group administration                     |
| [`ROLE_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_role-admin) | Server administration                             |
| [`SESSION_VARIABLES_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_session-variables-admin) | Server administration                             |
| [`SET_USER_ID`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_set-user-id) | Server administration                             |
| [`SHOW_ROUTINE`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_show-routine) | Server administration                             |
| [`SYSTEM_USER`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_system-user) | Server administration                             |
| [`SYSTEM_VARIABLES_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_system-variables-admin) | Server administration                             |
| [`TABLE_ENCRYPTION_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_table-encryption-admin) | Server administration                             |
| [`VERSION_TOKEN_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_version-token-admin) | Server administration                             |
| [`XA_RECOVER_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_xa-recover-admin) | Server administration                             |

# 图形化界面工具

- Workbench(免费): http://dev.mysql.com/downloads/workbench/
- navicat(收费，试用版30天): https://www.navicat.com/en/download/navicat-for-mysql
- Sequel Pro(开源免费，仅支持Mac OS): http://www.sequelpro.com/
- HeidiSQL(免费): http://www.heidisql.com/
- phpMyAdmin(免费): https://www.phpmyadmin.net/
- SQLyog: https://sqlyog.en.softonic.com/

# 安装

# 小技巧

1. 在SQL语句之后加上`\G`会将结果的表格形式转换成行文本形式
2. 查看Mysql数据库占用空间：
```mysql
SELECT table_schema "Database Name"
     , SUM(data_length + index_length) / (1024 * 1024) "Database Size in MB"
FROM information_schema.TABLES
GROUP BY table_schema;
```

# 后续内容

后续内容因为跟当前学习、工作计划有冲突，所以后续课程的学习计划会无限期推后。
目前的工作重点放在重做一个学习笔记网站，当然这是边做边学的，开发过程中遇到的难点和知识点我也会记录下来供大家学习。
**在此感谢B站同样爱学习的同学 @守心-人 提供的后续课程笔记，大家有条件一定要去给个star，你的每一个star和点赞都是我们前进的动力**
**[https://github.com/Buildings-Lei/mysql_note/blob/main/README.md](https://github.com/Buildings-Lei/mysql_note/blob/main/README.md)**





==**DML（data manipulation language）**==是数据操作语言：主要就是UPDATE、INSERT、DELETE这3种命令，是用来对数据库里的数据进行操作的语言。

**==DDL（data definition language）==**是数据定义语言：DDL比DML要多，主要的命令有CREATE、ALTER、DROP等，DDL主要是用在定义或改变**表（TABLE）**的结构，数据类型，表之间的链接和约束等初始化工作上，他们大多在建立表时使用。

==**CRUD（create read update delete)**==




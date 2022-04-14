# SQL学习笔记

Last update: 2022/4/12

FEATURE: NOT SENSITIVE ON UPPER & LOWER LETTER.

[TOC]

## INSERT:

```sql
INSERT INTO INSTRUCTOR VALUES ('10211', 'SMITH', 'BIOLOGY',66000 )
# WHICH IS EQUIVALENT TO
INSERT INTO INSTRUCTOR(ID, NAME, DEPT_NAME, SALARY) VALUES ('10211', 'SMITH', 'BIOLOGY',66000)
```

除了可以insert value，还可以将别的表select来的数据insert进去（格式要求相同）

```SQL
INSERT INTO INSTRUCTOR
	SELECT ID, NAME, DEPT_NAME, 19000
	FROM STUDENT
	WHERE DEPT_NAME = 'MUSIC' AND TOTAL_CRED > 144;
```

## UPDATE:

更新表中的所有数据：

```SQL
UPDATE INSTRUCTOR
	SET SALARY = SALARY * 1.05
```

更新表中符合条件的数据：

```SQL
UPDATE INSTRUCTOR
	SET SALARY = SALARY * 1.05
	WHERE SALARY < 70000
```

```SQL
UPDATE INSTRUCTOR
	SET SALARY = SALARY * 1.05
	WHERE SALARY < (SELECT AVG(SALARY) FROM INSTRUCTOR);
```

```SQL
# ORDER SEQUENCE IS ALSO IMPORTANT
UPDATE INSTRUCTOR
	SET SALARY = SALARY * 1.03
	WHERE SALARY > 100000
UPDATE INSTRUCTOR
	SET SALARY = SALARY * 1.05
	WHERE SALARY < 100000
```

```SQL
# CASE, LIKE IN VERILOG
UPDATE INSTRUCTOR
	SET SALARY = CASE
				WHEN SALARY <= 100000 THEN SALARY * 1.05
				ELSE SALARY * 1.03
				END
```



## DELETE:

只是删除所有数据，不删除格式

```sql
DELETE FROM STUDENT
```

删除特定符合条件的数据

```SQL
DELETE FROM STUDENT
WHERE DAD = 'LIGANG'
```

```SQL
DELETE FROM INSTRUCTOR WHERE DEPT_NAME IN (SELECT DEPT_NAME FROM DEPARTMENT WHERE BUILDING = TB)
```

```SQL
DELETE FROM INSTRUCTOR WHERE SALARY < (SELECT AVG(SALARY) FROM INSTRUCTOR)
```

## DROP:

数据连同格式一起删除

```SQL
DROP TABLE R
```

## ALTER:

```SQL
ALTER TABLE R ADD A D  # JOIN R WITH NEW ATTRIBUTE OF NAME A AND TYPE D
```

```SQL
ALTER TABLE R DROP A # DELETE CERTAIN COL OF TABLE R
```



## BASIC GRAMMAR:

```SQL
SELECT A1 FROM R1 WHERE ATTRIBUTE = 'XXX'
```

```SQL
SELECT * FROM R1
```

```SQL
SELECT DISTINCT A1 FROM R1
```

```SQL
SELECT '437' AS FOO # OUTCOME: 1X1 RESULT, '437' AS CONTEXT, FOO AS THE ONLY COL TITLE 
```

```SQL
SELECT 'ABC' FROM TEACHERS #ONE COL OF EQUAL LENGTH AS TABLE TEACHERS, FULL OF 'ABC'
```

```SQL
SELECT SALARY/12 FROM INSTRUCTORS # SUPPORT +, -, *, /, MOD(A,B) FOR A % B ON NUMERIC VAL
```

```SQL
SELECT NAME FROM INSTRUCTORS WHERE DEPT_NAME = 'MATH' AND SALARY > 90000
```

```SQL
# CARTESIAN PRODUCT
SELECT * FROM INSTRUCTORS, TEACHES;
# NATURAL JOIN
SELECT * FROM INSTRUCTORS, TEACHES WHERE INSTRUCTOR.ID = TEACHES.ID;
```

```SQL
#SELECT AND COMPARE IN ONE TABLE
SELECT DISTINCT T.NAME
FROM INSTRUCTOR T, INSTRUCTOR AS S  # KEYWORD 'S' CAN BE OMITTED, EQUIVALENT
WHERE T.SALARY > S.SALARY AND S.DEPT_NAME = 'BIOLOGY'
```

```SQL
SELECT NAME FROM INSTRUCTOR WHERE NAME LIKE '%DAR%'  # FIND NAME CONTAINING 'DAR', % FOR MANY CHARTERS
```

```SQL
SELECT NAME FROM INSTRUCTOR WHERE NAME LIKE '%D\%R%'  # FIND NAME CONTAINING 'D%R', BLACKSLASH \ TO DEBUFF
# SIMILARYLY: _ MATCHES EXACTLY ONE CHAR, _ _ _ % MATCHES AT LEAST 3 CHARS, 'AB%' IS STARTS WITH 'AB'
```

```SQL
SELECT DISTINCT NAME FROM INSTRUCTORS ORDER BY NAME # DEFAULT ASCENDING
SELECT DISTINCT NAME FROM INSTRUCTORS ORDER BY NAME, GENDER DESC # SORT ON 2 ATTRIBUTES
SELECT DISTINCT NAME FROM INSTRUCTORS ORDER BY NAME ASC # DEFAULT
```

```SQL
# FOR THOSE WHOSE 90000 <= SALARY <= 100000
SELECT NAME FROM INSTRUCTORS WHERE SALARY BETWEEN 90000 AND 100000
```

```SQL
# THE TYPE AND NAME (CAN BE CHANGED BY 'AS') OF THE ATTRIBUTE SHOULD BE THE SAME
(SELECT NAME FROM CRAZY_PLAYER)
UNION
(SELECT NAME FROM GOOD_STUDENT);

(SELECT NAME FROM CRAZY_PLAYER)
INTERSECT
(SELECT NAME FROM GOOD_STUDENT);

(SELECT NAME FROM CRAZY_PLAYER)
EXCEPT
(SELECT NAME FROM GOOD_STUDENT);
#BY DEFAULT, NO DUMPLICATES.
#OTHERWISE, USE KEYWORD 'UNION ALL' 'INTERSECT ALL' 'EXCEPT ALL'
```

```SQL
5 + NULL # NULL
5 < NULL # UNKNOWN
NULL <> NULL # UNKNOWN
NULL = NULL # UNKNOWN
# UNKNOWN CAN BE EITHER TRUE OR FALSE.
(TRUE AND UNKNOWN) # UNKNOWN
(FALSE AND UNKNOWN) # FALSE
(UNKNOWN AND UNKNOWN) # UNKNOWN
(TRUE OR UNKNOWN) # TRUE
(FALSE OR UNKNOWN) # UNKNOWN
(UNKNOWN OR UNKNOWN) # UNKNOWN
# IF UNKNOWN APPEARS IN WHERE CLAUSE, CAN BE REGARDED AS 'FALSE'
```

```SQL
SELECT NAME FROM INSTRUCTOR WHERE SALARY IS NULL
```

```SQL
SELECT AVG(SALARY) FROM INSTRUCTORS
SELECT COUNT(DISTINCT ID) FROM INSTRUCTORS
SELECT COUNT(*) FROM COURSES
SELECT MIN(SALARY) FROM INSTRUCTORS
SELECT SUM(SALARY) FROM INSTRUCTORS
```

```SQL
# CALCULATE EACH DEPT'S INSTRUCTORS' AVG_SALARY
SELECT DEPT_NAME, AVG(SALARY) AS AVG_SALARY
FROM INSTRUCTORS
GROUP BY DEPT_NAME
# CAUTION: "SELECT DEPT_NAME, ID .... GROUP BY DEPT_NAME" IS ILLEGAL, "ID" SHOULD NOT APPEAR
```

```SQL
SELECT DEPT_NAME, AVG(SALARY) AS AVG_SALARY
FROM INSTRUCTORS
GROUP BY DEPT_NAME HAVING AVG(SALARY) > 42000
# "HAVING" IS SIMILAR TO "WHERE", BUT APPEARS IN "GROUP BY"
```

```SQL
# SET MEMBERSHIP
SELECT DISTINCT COURSE_ID FROM SECTION
WHERE COURSE_ID (NOT) IN (SELECT COURSE_ID FROM ALL_COURSES)
```

```SQL
# SOME - COMPARE INSIDE ONE TABLE, SELECT INSTRUCTORS MORE THAN LEAST IN BIO DEPT
# ACTUALLY, 'SOME' REALLY STANDS FOR SOME.
SELECT NAME FORM INSTRUCTOR
WHERE SALARY > SOME(SELECT SALARY FROM INSTRUCTOR WHERE DEPT_NAME = 'BIOLOGY')
```

```SQL
# SIMILARYLY, ALL APPEARS
SELECT NAME FROM INSTRUCTOR
WHERE SALARY > ALL(SELECT SALARY FROM INSTRUCTOR WHERE DEPT_NAME = 'BIOLOGY')
```

```SQL
# IF "EXIST R" AND R IS NOT EMPTY THEN EQUIVALENT TO "TRUE"
# THE NAME "S" CAN BE INHERITED IN "WHERE" SUBQUERY
SELECT COURSE_ID FROM SECTION AS S
WHERE SEMESTER = 'FALL' AND YEAR = 2017 AND 
	EXISTS (SELECT * FROM SECTION AS T WHERE SEMESTER = 'SPRING' AND YEAR = '2018'
											AND S.COURSE_ID = T.COURSE_ID)
```

```SQL
# FIND ALL STUDENTS WHO HAVE TAKEN ALL COURSES OFFERED IN THE BIOLOGY DEPT.
SELECT DISTINCT S.ID, S.NAME
FROM STUDENT AS S
WHERE NOT EXISTS ((SELECT COURSE_ID FROM COURSE WHERE DEPT_NAME = 'BIOLOGY')
					EXCEPT
					(SELECT T.COURSE_ID FROM TAKES AS T WHERE S.ID = T.ID));
```

```SQL
# FIND ALL COURSES THAT WERE OFFERED AT MOST ONCE IN 2017
# THE SELECT ALLOWS DUMPLICATES BY DEFAULT. OTHERWISE, USE DISTINCT.
SELECT T.COURSE_ID FROM COURSE AS T
WHERE UNIQUE(SELECT R.COURSE_ID FROM SECTION AS R WHERE T.COURSE_ID = R.COURSE_ID AND R.YEAR = 2017)
# NOTE: UNIQUE(0) = TRUE, UNIQUE(1) = TRUE
```

```SQL
# WITH CAN CREATE A TEMPORARY RELATION, ONLY VALID IN THIS SQL
WITH MAX_BUDGET(VALUE) AS (SELECT MAX(BUDGET) FROM DEPARTMENT)
SELECT DEPARTMENT.NAME
FROM DEPARTMENT, MAX_BUDGET
WHERE DEPARTMENT.BUDGET = MAX_BUDGET.VALUE
```

```sql
ROUND(x,2) # 将x保留两位小数
SUM(ATTRIBUTE) # 累加某个attribute，通常出现在group by从句
SUM(IF(T.STATUS = 'completed', 0,1)) # 如果满足条件则等效替换为1，否则0.
```

```sql
# <> equivalent to !=
```



## Join

### Natural Join # 警告：Mysql不支持natural join，等效替换inner join

前面提到的insert是增加行。如果要增加列，一般用join等方法。

```SQL
# 在这里，name是student里的attribute，course_id是takes的attribute
SELECT NAME, COURSE_ID FROM STUDENT, TAKES WHERE STUDENT.ID = TAKES.ID;
# 同时，也可以用natural join表示
SELECT NAME, COURSE_ID FROM STUDENT NATURAL JOIN TAKES;
```

但natural join在某种情况下会变得很危险。

natural join的匹配机制是，假如A表格有a，b，c三个attribute，B表格有a, b, e, f四个attribute。此时因为a和b在两个表格中都存在，所以系统会筛选出a和b完全match的一副tuple拼接在一起。若在这种情况下，我们不希望全部匹配（比如说我只是希望match a 这个pair），那么就要手工通过where A.a = B.a 进行匹配，不可以用natural join。

### Outer Join

效果：减少数据损失，即对natura join产生问题的解决方案。

#### Left Outer Join # Mysql支持，等同于left join

还是刚刚的假设，假如A表格有a，b，c三个attribute，B表格有a, b, e, f四个attribute。此时因为a和b在两个表格中都存在，我们要求在**左边**的tuple**全部保留**，并尽可能地寻找右边match的pair，如果都能在a,b这两个attribute上完美match就最好了，没有的话还是要把左边留着，右边的内容就用null替代。剩下右边无法与左边匹配的就扔了吧。

#### Right Outer Join # Mysql支持，等同于right join

和上面的类似，只是将左换成右

#### Full Outer Join  # Mysql 中不支持

和上面的类似，区别在于对匹配不到的结果的处理：不是直接扔掉，而是sign上null后保留在末尾，相当于将所有（不完美匹配a和b的）可能性都加上去了，只是缺失的部分用null填满。（设为代表未知的null）



## Views

### Introduction

相当于python中的def，实现节约代码量的目标

```sql
create view department_total_salary(dept_name, total_salary) as
	select dept_name, sum(Salary) as total_salary
	from instructor
	group by dept_name
```

假设v1，v2，v3都是views，那么；

如果v1内含v2，叫做v1 depend directly on v2

如果v1内含v2，或者v1内含v3，v3内含v2，叫做v1 depend on v2

如果v1内含v1，那么就叫recursive

### Materialized Views

指的是有些数据库系统在views被初次定义的时候，就一次性拉取了table并储存了起来，有别于其他实时分析拉取的views方法。

那么我们就要保证，当views所拉取的tables的内容发生改变时，views也得重新拉取并update/alter。

大部分的数据库支持对views进行insert/update，前提是这个只是一个simple views。

simple views的定义：

1. from语句内只有一个database relation
2. select语句内只有一个attribute，且不包含其他的表达式、集合或是distinct声明
3. 没有group by或having声明



## Transactions

### Introduction

transaction是指多个query 和/或 update的声明组合成的集合，是一个大型功能块。

当执行第一个query的时候，就算开始了一段transaction。

结束有两种方式：

1. commit work，提交并保存
2. rollback work，不保存并退出

Atomic transaction：指要么完全执行完成，要么不保存就退出（指没有跳转到别的transaction去）

同时，我们要对同时进行的transaction进行保障。

### Integrity constraints

系统设置了很多Integrity constraints完整性约束，防止数据出错。

典型案例：

#### not null

该attribute中禁止出现null值，常出现在primary key中（因为pk不允许有null值）

#### unique

unique(A1,A2,A3) 指A1,A2,A3可以形成一个candidate key，有别于primary key的是，可以含有null值。

#### check

example:

```sql
create table section 
    (course_id varchar (8),
    sec_id varchar (8),
    semester varchar (6),
    year numeric (4,0),
    building varchar (15),
    room_number varchar (7),
    time slot id varchar (4), 
    primary key (course_id, sec_id, semester, year),
    check (semester in ('Fall', 'Winter', 'Spring', 'Summer')))
```

check规则在创建table的时候就应该declare，在后续的数据insert/update中，要求满足条件方可进行，否则拒绝请求。

下面的sql是在创建table `section`时的语句：

```SQL
CHECK (TIME_SLOT_ID IN (SELECT TIME_SLOT_ID FROM TIME_SLOT))
```

在检查上面的条件时，不仅需要在每次insert的时候重新拉取subquery，而且当`time_slot`这张tableupdate的时候，需要检查原先`section`中的数据是否符合新规则，若不符合则拒绝请求。

### Referential Integrity

又名参照完整性，指的是若某个数据在一个table(relation)里出现，就一定要在另一个table(relation)里出现。

#### Foreign key

假如说R和S是两个relation，都包含A这个attribute（A是S的pk, primary key）。假如说一个值必须要先出现在S，才能出现在R，那么我们把它称为 A is a foreign key of R (A是R的外键)

 一般来说，被引的那个foreign key都是对方的primary key。

```SQL
FOREIGN KEY(DEPT_NAME) REFERENCES DEPARTMENT (DEPT_NAME)
# 简略版：
FOREIGN KEY(DEPT_NAME) REFERENCES DEPARTMENT 
```



## Data type

常见的datatype: varchar, numeric, int, ...

### Built-in Data Types

date

```sql
date '2020-7-27'
```

*Example*

```sql
DATEDIFF(w1.RecordDate, w2.RecordDate) # == w1 - w2的天数
```

time

```sql
time '09:00:30'
```

timestamp

```sql
timestamp '2020-7-27 09:00:30'
```

### Large-Object Types

可以用于存放图片，视频等大文件。需要注意的是，query返回的是一个指向大文件的指针。

#### 以二进制(binary code)形式存放

`blob`

#### 以字符(char)形式存放

`clob`

### User-Defined Types

用户可以方便地自定义类型。在创建table时须提前声明。

```sql
create type Dollars as numeric (12,2) final

create table department
(dept_name varchar (20),
building varchar (15),
budget Dollars);
```

### Domains

在user-defined的基础上，加多了attribute的constraint（check, not null, foreign key etc.)。

```sql
create domain person_name char(20) not null
```

### Index

```SQL
CREATE INDEX STUDENT_INDEX ON STUDENT(ID)
```

优点：加快用户query搜索速度

缺点：减慢数据库insert/update更新速度

常用于primary key等经常被搜索的值上，用户不可见，隐性加速。



## Authorization

用户可能拥有不同的权限：

- select - 读取
- Insert - 插入
- Update - 更改
- Delete - 删除
- all privileges - 全部权限

一个用户可以拥有以上所有权限，也可能在某个relation中权限受限。

利用grant语句赋权，格式是 grant + 权限 + on + table名 + to + 用户名

```sql
grant select, insert on department to Alex, Mike
```

但这不意味者被赋权的用户拥有该table所引用的子表的相对应权限。

利用rewoke语句撤销权限。

```sql
revoke select, insert on department from Alex, Mike
```

可以创建用户组(role)，方便对用户赋权。

用户组可以叠加，形成层级结构。

```sql
create role instructor
create role president
grant instructor to Yangzhou_Deng
grant president to Yangsheng_Xu
grant instructor to president # President has all privilege of instructors
grant select on student_info to instructor
grant delete on student_info to instructor 
```



## 一些题目

[Leetcode 1667](https://leetcode-cn.com/problems/fix-names-in-a-table/)

编写一个 SQL 查询来修复名字，使得只有第一个字符是大写的，其余都是小写的。

返回按 `user_id` 排序的结果表。

```sql
select  
    user_id,  CONCAT(upper(left(name, 1)), lower(substr(name, 2))) name
from 
    users
order by 
    user_id;
```

用left函数取名字首字符转为大写，用substr取名字第二个及之后所有字符转为小写，再拼接即可。 `substr(string,pos,end)`这个函数不填入end就是取pos位置及其之后所有的字符。



[Leetcode 1484](https://leetcode-cn.com/problems/group-sold-products-by-the-date/)

编写一个 SQL 查询来查找每个日期、销售的不同产品的数量及其名称。
每个日期的销售产品名称应按词典序排列。
返回按 sell_date 排序的结果表。

```sql
select
    sell_date,
    count(distinct product) num_sold,
    GROUP_CONCAT(distinct product) products
from
    activities
group by sell_date
order by sell_date;
```

GROUP_CONCAT可以将所有位于该组的单元连在一起，并用逗号隔开。如`"Basketball,Headphone,T-Shirt"`



[Leetcode 176](https://leetcode-cn.com/problems/second-highest-salary/)

编写一个 SQL 查询，获取并返回 `Employee` 表中第二高的薪水 。如果不存在第二高的薪水，查询应该返回 `null` 。

```sql
select ifNull((
select distinct Salary
from Employee
order by Salary desc limit 1,1),null) as SecondHighestSalary
```

limit y offset x 分句表示查询结果跳过 x 条数据，读取前 y 条数据



[Leetcode 178](https://leetcode-cn.com/problems/rank-scores/)

编写 SQL 查询对分数进行排序。排名按以下规则计算:

分数应按从高到低排列。
如果两个分数相等，那么两个分数的排名应该相同。
在排名相同的分数后，排名数应该是下一个连续的整数。换句话说，排名之间不应该有空缺的数字。
按 score 降序返回结果表。

```sql
SELECT
    score,
    dense_rank() over (order by Score desc) AS "rank" 
FROM
    Scores;
```

*固定组合：*rank + over + order

rank() over (order by Score desc)：排名相同的两名是并列，但是占两个名次，1 1 3 4 4 6这种

dense_rank() over (order by Score desc)：排名相同的两名是并列，共占一个名词，1 1 2 3 3 4这种

row_number() over(order by Score desc)这个函数不需要考虑是否并列，哪怕根据条件查询出来的数值相同也会进行连续排名 1 2 3 4 5



[Leetcode 601](https://leetcode-cn.com/problems/human-traffic-of-stadium/)

编写一个 SQL 查询以找出每行的人数大于或等于 `100` 且 `id` 连续的三行或更多行记录。

返回按 `visit_date` **升序排列** 的结果表。

```sql
SELECT 
    id, visit_date, people
FROM (
     SELECT 
        id, visit_date, people, count(*) over(partition by tag) as cnt_tag
     FROM (
        SELECT 
            id, visit_date, people, id - row_number() over(order by id) as tag
        FROM 
            stadium
        WHERE 
            people >= 100
    ) t
) y
WHERE 
    cnt_tag >= 3
ORDER BY id;
```

关键点在`id - row_number() over(order by id) as tag`这里，在上一轮搜索中，已经筛掉了那些人数小于100人的记录，此时再手动加上id的话，会在被筛掉的间隙产生“断层”——断层和断层之间的`id - row_number() over(order by id) as tag`是一样的，也就是说，我们可以通过这个，来筛选紧密相连的记录。



[Leetcode 1097](https://leetcode-cn.com/problems/game-play-analysis-v/)

count() 并不会将null计入。

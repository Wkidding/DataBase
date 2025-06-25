# 题目

[196. 删除重复的电子邮箱](https://leetcode.cn/problems/delete-duplicate-emails/)

表: `Person`

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| email       | varchar |
+-------------+---------+
id 是该表的主键列(具有唯一值的列)。
该表的每一行包含一封电子邮件。电子邮件将不包含大写字母。
```

编写解决方案 **删除** 所有重复的电子邮件，只保留一个具有最小 `id` 的唯一电子邮件。

（对于 SQL 用户，请注意你应该编写一个 `DELETE` 语句而不是 `SELECT` 语句。）

（对于 Pandas 用户，请注意你应该直接修改 `Person` 表。）

运行脚本后，显示的答案是 `Person` 表。驱动程序将首先编译并运行您的代码片段，然后再显示 `Person` 表。`Person` 表的最终顺序 **无关紧要** 。

返回结果格式如下示例所示。

**示例 1:**

```
输入: 
Person 表:
+----+------------------+
| id | email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
| 3  | john@example.com |
+----+------------------+
输出: 
+----+------------------+
| id | email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
+----+------------------+
解释: john@example.com重复两次。我们保留最小的Id = 1。
```

# 思路

本题中，需要注意的是，删除操作。可以分为两步：

1、首先按照题目条件查出相应记录，可以使用自连接方式查询

2、使用第一步中的查询条件，做delete操作。



这种DELETE方式很陌生，竟然和SELETE的写法类似。它涉及到t1和t2两张表，DELETE t1表示要删除t1的一些记录，具体删哪些，就看WHERE条件，满足就删；

这里删的是t1表中，跟t2匹配不上的那些记录。

所以，官方sql中，DELETE p1就表示从p1表中删除满足WHERE条件的记录。

2、p1.Id > p2.Id

继续之前，先简单看一下表的连接过程，这个搞懂了，理解WHERE条件就简单了👇

a. 从驱动表（左表）取出N条记录；
b. 拿着这N条记录，依次到被驱动表（右表）查找满足WHERE条件的记录；



```mysql
-- 解法一：DELETE + 自连接
delete p1
from Person p1
inner join Person p2
where p1.email = p2.email and p1.id>p2.id;

-- 解法二：DELETE + 子查询
DELETE FROM Person
WHERE Id NOT IN (   -- 删除不在查询结果中的值
    SELECT id FROM
   (
       SELECT MIN(Id) AS Id -- 排除Email相同时中Id较大的行
       FROM Person
       GROUP BY Email
   ) AS temp    -- 此处需使用临时表，否则会发生报错
)
```



## 本题要点 

1. **delete语法如何作用于联结表** 2. **inner join的原理**

1. delete语法

最原始的delete语句

```mysql
delete from table1 where table1.id = 1;
```

如果需要关联其他表进行删除

```mysql
delete table1 
from table1 inner join table2 on table1.id = table2.id 
where table2.type = 'something' and table1.id = 'idnums';
```



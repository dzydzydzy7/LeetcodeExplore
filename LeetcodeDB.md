## 175、组合两个表

表1: Person

```
+-------------+---------+
| 列名         | 类型     |
+-------------+---------+
| PersonId    | int     |
| FirstName   | varchar |
| LastName    | varchar |
+-------------+---------+
```


PersonId 是上表主键
表2: Address

```
+-------------+---------+
| 列名         | 类型    |
+-------------+---------+
| AddressId   | int     |
| PersonId    | int     |
| City        | varchar |
| State       | varchar |
+-------------+---------+
```


AddressId 是上表主键


编写一个 SQL 查询，满足条件：无论 person 是否有地址信息，都需要基于上述两表提供 person 的以下信息：

```
FirstName, LastName, City, State
```

**解法**：

很明显是个left join：

```mysql
select FirstName, LastName, City, State from Person left join Address on Person.PersonId = Address.PersonId
```

## 181、超过经理收入的员工

Employee 表包含所有员工，他们的经理也属于员工。每个员工都有一个 Id，此外还有一列对应员工的经理的 Id。

```
+----+-------+--------+-----------+
| Id | Name  | Salary | ManagerId |
+----+-------+--------+-----------+
| 1  | Joe   | 70000  | 3         |
| 2  | Henry | 80000  | 4         |
| 3  | Sam   | 60000  | NULL      |
| 4  | Max   | 90000  | NULL      |
+----+-------+--------+-----------+
```


给定 Employee 表，编写一个 SQL 查询，该查询可以获取收入超过他们经理的员工的姓名。在上面的表格中，Joe 是唯一一个收入超过他的经理的员工。

```
+----------+
| Employee |
+----------+
| Joe      |
+----------+
```

**解法**：

```mysql
select e1.Name as Employee from Employee as e1 inner join Employee as e2 on e1.ManagerId = e2.id where e1.Salary > e2.Salary;
```

把where换成and会更快

```mysql
select e1.Name as Employee from Employee as e1 inner join Employee as e2 on e1.ManagerId = e2.id and e1.Salary > e2.Salary;
```

## 183、从不订购的客户

某网站包含两个表，Customers 表和 Orders 表。编写一个 SQL 查询，找出所有从不订购任何东西的客户。

Customers 表：

```
+----+-------+
| Id | Name  |
+----+-------+
| 1  | Joe   |
| 2  | Henry |
| 3  | Sam   |
| 4  | Max   |
+----+-------+
```


Orders 表：

```
+----+------------+
| Id | CustomerId |
+----+------------+
| 1  | 3          |
| 2  | 1          |
+----+------------+
```


例如给定上述表格，你的查询应返回：

```
+-----------+
| Customers |
+-----------+
| Henry     |
| Max       |
+-----------+
```

**解法**：

使用子查询和 not in。

```mysql
select `Name` as Customers from Customers where Customers.id not in (
    select `CustomerId` from Orders
);
```

巧妙使用左连接，如果没有买过的在Order表的部分会为null

```mysql
select `Name` as Customers 
from Customers c left join Orders o 
on c.Id = o.CustomerId 
where o.Id is null;
```

## 197、上升的温度

给定一个 Weather 表，编写一个 SQL 查询，来查找与之前（昨天的）日期相比温度更高的所有日期的 Id。

```
+---------+------------------+------------------+
| Id(INT) | RecordDate(DATE) | Temperature(INT) |
+---------+------------------+------------------+
|       1 |       2015-01-01 |               10 |
|       2 |       2015-01-02 |               25 |
|       3 |       2015-01-03 |               20 |
|       4 |       2015-01-04 |               30 |
+---------+------------------+------------------+
```


例如，根据上述给定的 Weather 表格，返回如下 Id:

```
+----+
| Id |
+----+
|  2 |
|  4 |
+----+
```

**解法：**

条件连接

```mysql
select w2.Id from Weather as w1, Weather as w2 where w1.Temperature < w2.Temperature and datediff(w2.RecordDate, w1.RecordDate) = 1 -- w2是后一天
```

join连接

```mysql
select w2.Id from Weather as w1 join Weather as w2 on datediff(w2.RecordDate, w1.RecordDate) = 1 where w1.Temperature < w2.Temperature  -- w2是后一天
```

## 182、寻找重复的电子邮箱

编写一个 SQL 查询，查找 Person 表中所有重复的电子邮箱。

示例：

```
+----+---------+
| Id | Email   |
+----+---------+
| 1  | a@b.com |
| 2  | c@d.com |
| 3  | a@b.com |
+----+---------+
```


根据以上输入，你的查询应返回以下结果：

```
+---------+
| Email   |
+---------+
| a@b.com |
+---------+
```


说明：所有电子邮箱都是小写字母。

**解法：**

自连接查询：

```mysql
select distinct(p1.Email) from Person as p1 join Person as p2 
on p1.Id != p2.Id where p1.Email = p2.Email 
```

使用group by：

```mysql
select Email from (
    select Email, count(Email) as num
    from Person group by Email
) as tmp where num > 1 -- 每个派生的表必须有别名
```

使用 group by 和 having：

```mysql
select Email from Person group by Email having count(Email) > 1
```

## 595、大的国家

这里有张 World 表

```
+-----------------+------------+------------+--------------+---------------+
| name            | continent  | area       | population   | gdp           |
+-----------------+------------+------------+--------------+---------------+
| Afghanistan     | Asia       | 652230     | 25500100     | 20343000      |
| Albania         | Europe     | 28748      | 2831741      | 12960000      |
| Algeria         | Africa     | 2381741    | 37100000     | 188681000     |
| Andorra         | Europe     | 468        | 78115        | 3712000       |
| Angola          | Africa     | 1246700    | 20609294     | 100990000     |
+-----------------+------------+------------+--------------+---------------+
```


如果一个国家的面积超过300万平方公里，或者人口超过2500万，那么这个国家就是大国家。

编写一个SQL查询，输出表中所有大国家的名称、人口和面积。

例如，根据上表，我们应该输出:

```
+--------------+-------------+--------------+
| name         | population  | area         |
+--------------+-------------+--------------+
| Afghanistan  | 25500100    | 652230       |
| Algeria      | 37100000    | 2381741      |
+--------------+-------------+--------------+
```

**解法：**

普通的单表查询：

```mysql
select `name`, `population`, `area` from World 
where `area` > 3000000 or `population` > 25000000 
```

使用union

```mysql
select `name`, `population`, `area` from World 
where `area` > 3000000 
union	-- 连接两个select的共有的结果，会去除重复列
select `name`, `population`, `area` from World 
where `population` > 25000000 
```

## 620、有趣的电影

某城市开了一家新的电影院，吸引了很多人过来看电影。该电影院特别注意用户体验，专门有个 LED显示板做电影推荐，上面公布着影评和相关电影描述。

作为该电影院的信息部主管，您需要编写一个 SQL查询，找出所有影片描述为非 boring (不无聊) 的并且 id 为奇数 的影片，结果请按等级 rating 排列。

例如，下表 cinema:

```
+---------+-----------+--------------+-----------+
|   id    | movie     |  description |  rating   |
+---------+-----------+--------------+-----------+
|   1     | War       |   great 3D   |   8.9     |
|   2     | Science   |   fiction    |   8.5     |
|   3     | irish     |   boring     |   6.2     |
|   4     | Ice song  |   Fantacy    |   8.6     |
|   5     | House card|   Interesting|   9.1     |
+---------+-----------+--------------+-----------+
```


对于上面的例子，则正确的输出是为：

```
+---------+-----------+--------------+-----------+
|   id    | movie     |  description |  rating   |
+---------+-----------+--------------+-----------+
|   5     | House card|   Interesting|   9.1     |
|   1     | War       |   great 3D   |   8.9     |
+---------+-----------+--------------+-----------+
```

**解法**：

```mysql
select * from cinema where not description = 'boring' and id % 2 <> 0 order by rating desc;  -- 升序是asc
```

可以使用mod函数：

```mysql
select * from cinema where not description = 'boring' and mod(id, 2) = 1 order by rating desc;
```



## 627、交换工资

给定一个 `salary` 表，如下所示，有 `m = 男性` 和 `f = 女性` 的值。交换所有的 `f` 和 `m` 值（例如，将所有 f 值更改为 m，反之亦然）。要求只使用一个更新（Update）语句，并且没有中间的临时表。

注意，您必只能写一个 Update 语句，请不要编写任何 Select 语句。

例如：

| id   | name | sex  | salary |
| ---- | ---- | ---- | ------ |
| 1    | A    | m    | 2500   |
| 2    | B    | f    | 1500   |
| 3    | C    | m    | 5500   |
| 4    | D    | f    | 500    |
运行你所编写的更新语句之后，将会得到以下表:

| id   | name | sex  | salary |
| ---- | ---- | ---- | ------ |
| 1    | A    | f    | 2500   |
| 2    | B    | m    | 1500   |
| 3    | C    | f    | 5500   |
| 4    | D    | m    | 500    |

**解法**：

使用 case-when 流程控制语句:

```mysql
update salary set `sex` = 
case `sex` when 'm' then 'f' else 'm' end; 
```

使用ASCII码：

```mysql
update salary set `sex` = char(ascii('m') + ascii('f') - ascii(`sex`));
```


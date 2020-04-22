## 常用命令

开启 MySQL 服务：`mysqld`
连接 MySQL：`mysql -h 主机名 -P 端口号 -u 用户名 -p`

查看所有数据库：`SHOW DATABASES;`
进入数据库：`USE 数据库名;`
查看当前进入的数据库：`SELECT DATABASE();`

查看数据库中所有表：`SHOW TABLES;`、`SHOW TABLES FROM 表名;`
查看表结构：`DESC 表名;`

退出：`EXIT`

查看数据库版本：`SELECT VERSION();`、`mysql --version`、`mysql -V`






## 查询数据

### 普通查询

```sql
# 查询所有数据
SELECT * FROM `person`;

# 查询指定数据
SELECT `first_name`,`last_name` FROM `person`;

# 重命名列名
SELECT `first_name` AS `FirstName`,`last_name` AS `LastName` FROM `person`;

# 统计查询结果的行数
SELECT COUNT(*) FROM `person`;

# 去除重复的查询结果
SELECT DISTINCT `name` FROM `person`;

# 拼接查询结果
SELECT CONCAT(`id`, ',', `name`) AS 'Result' FROM `person`;
```

### 条件查询

条件运算符：
<table>
<tr>
<td>></td>
<td><</td>
<td>=</td>
<td>!=</td>
<td><></td>
<td>>=</td>
<td><=</td>
</tr>
</table>

逻辑运算符：
<table>
<tr>
<td>&&</td>
<td>||</td>
<td>!</td>
</tr>
<tr>
<td>AND</td>
<td>OR</td>
<td>NOT</td>
</tr>
</table>

模糊查询：
<table>
<tr>
<td>LIKE</td>
<td>BETWEEN AND</td>
<td>IN</td>
<td>IS NULL</td>
</tr>
</table>

通配符：
<table>
<tr>
<td>%</td>
<td>替代 0 个或多个字符</td>
</tr>
<tr>
<td>_</td>
<td>替代一个字符</td>
</tr>
</table>

```sql
# 条件查询
SELECT * FROM `person` WHERE `id` = 1;

# BETWEEN AND 包含临界值
SELECT * FROM `person` WHERE `id` BETWEEN 1 AND 10;

# 转义字符
SELECT * FROM `person` WHERE `name` LIKE '_\_a';
=
SELECT * FROM `person` WHERE `name` LIKE '_$_a' ESCAPE '$';

# IN 语句相比 OR 语句更加简洁，条件所在列没有索引的话，IN 效率高于 OR。
SELECT * FROM `person` WHERE `name` = 'Jack' OR `name` = 'Tom';
=
SELECT * FROM `person` WHERE `name` IN ('Jack', 'Tom');

# 判断 NULL
SELECT * FROM `person` WHERE `name` IS NULL;
SELECT * FROM `person` WHERE `name` IS NOT NULL;
```

### 排序查询

```sql
# 正序排序
SELECT * FROM `person` ORDER BY `id` ASC;

# 倒序排序
SELECT * FROM `person` ORDER BY `id` DESC;

# 按字符长度排序
SELECT LENGTH(`name`) AS `name_length`, `id`, `name`
FROM `person`
ORDER BY `name_length` ASC;
```

### 分组查询

`GROUP BY`：分组。

`HAVING`：分组后筛选。

```sql
# 查询每个位置上部门的个数
SELECT location_id, COUNT(*)
FROM department
GROUP BY location_id;

# 查询邮箱中包含 a 字符，每个部门的平均工资
SELECT department_id, AVG(salary)
FROM employee
WHERE email LIKE '%a%'
GROUP BY department_id;

# 根据结果进行筛选，查询哪个部门的员工个数 > 2
SELECT department_id, COUNT(*)
FROM employee
GROUP BY department_id
HAVING COUNT(*) > 2;
```

### 连接查询（多表查询）

笛卡尔乘积：当查询多张表时，没有添加有效的条件，导致多张表所有行都完全连接。

#### SQL 92 标准：

- 内连接

  - 等值连接

    ```sql
    SELECT 查询列表
    FROM 表1 AS 别名, 表2 AS 别名
    WHERE 表1.key = 表2.key
    [AND 筛选条件]
    [GROUP BY 分组字段]
    [HAVING 分组后的筛选条件]
    [ORDER BY 排序字段]
    ```

    

  - 非等值连接

    ```sql
    SELECT 查询列表
    FROM 表1 AS 别名, 表2 AS 别名
    WHERE 非等值的连接条件
    [AND 筛选条件]
    [GROUP BY 分组字段]
    [HAVING 分组后的筛选条件]
    [ORDER BY 排序字段]
    ```

    

  - 自连接

    ```sql
    SELECT 查询列表
    FROM 表 AS 别名1, 表 AS 别名2
    WHERE 自连接的连接条件
    [AND 筛选条件]
    [GROUP BY 分组字段]
    [HAVING 分组后的筛选条件]
    [ORDER BY 排序字段]
    ```

    

- 外连接（Oracle、SQL Server 支持，MySQL 不支持）

#### SQL 99 标准【推荐使用】：

```sql
SELECT 查询列表
FROM 表1 AS 别名
JOIN 表2 AS 别名
ON 连接条件
[WHERE 筛选条件]
[GROUP BY 分组字段]
[HAVING 筛选条件]
[ORDER BY 排序字段]
```



- 内连接：`INNER JOIN`

- 外连接
  
  > 应用场景：用于查询一个表中有，另一个表没有的记录。
  >
  > ---
  >
  > 特点：
  >
  > 外连接查询结果为主表中所有记录；
  >
  > 如果从表中有和主表匹配的值，则显示匹配的值；
  >
  > 如果从表中没有和主表匹配的值，则显示 NULL；
  >
  > 外连接查询结果 = 内连接结果 + 主表中有而从表中没有的记录；
  >
  > ---
  >
  > 左外连接：LEFT JOIN 左边的是主表。
  >
  > 右外连接：RIGHT JOIN 右边的是主表。
  >
  > ---
  >
  > 左外连接和右外连接交换两个表的顺序，可以实现同样的效果。
  
  - 左外连接：`LEFT JOIN`
  - 右外连接：`RIGHT JOIN`
  - 全外连接（MySQL不支持）：`FULL JOIN`
  
- 交叉连接：`CROSS JOIN`，查询结果为笛卡尔乘积。

### 子查询（内查询）

- 含义

  出现在其它语句中的`SELECT`语句，称为子查询或内查询。外部的查询语句，称为主查询或外查询。

- 分类

  - 按子查询出现的位置

    - `SELECT`后面：支持标量子查询。

    - `FROM`后面：支持表子查询。

    - `WHERE`或`HAVING`后面：支持标量子查询、列子查询、行子查询。

      > | 操作符              | 含义                       |
      > | ------------------- | -------------------------- |
      > | IN / NOT IN（常用） | 等于列表中任意一个         |
      > | ANY / SOME          | 和子查询返回的某一个值比较 |
      > | ALL                 | 和子查询返回的所有值比较   |

    - `EXISTS`后面（相关子查询）：支持表子查询。

  - 按结果集的行列数不同

    - 标量子查询（结果集只有一行一列）
    - 列子查询（结果集只有一列多行）
    - 行子查询（结果集有一行多列）
    - 表子查询（结果集一般为多行多列）

### 分页查询

```sql
SELECT 查询列表
FROM 表
[WHERE 筛选条件]
[GROUP BY 分组字段]
[HAVING 分组后的筛选]
[ORDER BY 排序字段]
LIMIT offset, size; # 分页
```

`offset`：要显示条目的起始索引（起始索引从0开始）

`size`：要显示的条目个数

### 联合查询

`UNION`：连接两个查询语句，去除相同结果。

`UNION ALL`：连接两个查询语句，不去除相同结果。

```sql
SELECT * FROM 表 WHERE 筛选条件
UNION
SELECT * FROM 表 WHERE 筛选条件;
```





## 插入数据

```sql
INSERT INTO 表 VALUES (值,值);

INSERT INTO 表 (列,列) VALUES (值,值);

INSERT INTO 表 SET 列=值, 列=值;

INSERT INTO 表1 SELECT * FROM 表2;
```





## 更新数据

### 单表更新

```sql
UPDATE 表 SET 列=值, 列=值 [WHERE 列=值];
```

### 多表更新

```sql
UPDATE 表1
INNER|LEFT|RIGHT JOIN 表2
ON 连接条件
SET 列名=值, 列名=值
[WHERE 筛选条件];
```





## 删除数据

### 单表删除

```sql
DELETE FROM 表 WHERE 删除条件;
```

### 多表删除

```sql
DELETE 表, 表2 # 要删除的表
FROM  表
INNER|LEFT|RIGHT JOIN 表2
ON 连接条件
WHERE 删除条件
```

删除表中所有数据：

```sql
TRUNCATE TABLE 表;
```

```sql
DELETE FROM 表;
```

区别：

- 效率较高，不能加 WHERE 条件

- 如果用 DELETE 删除后，再插入数据，自增长列的值从断点开始

- 如果用 TRUNCATE 删除后，再插入数据，自增长列的值从 1 开始

- TRUNCATE 没有返回值，DELETE 有返回值

- 在事务中，TRUNCATE 不能回滚，DELETE 可以回滚





## 库和表的管理

### 创建库

```sql
CREATE DATABASE [IF NOT EXISTS] 库名 [DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci];
```

### 更改库的字符集

```sql
ALTER DATABASE 库名 CHARACTER SET utf8;
```

### 删除库

```sql
DROP DATABASE [IF EXISTS] 库名;
```

### 创建表

```sql
CREATE TABLE 表名(
    列名 列类型[(长度) 约束],
    列名 列类型[(长度) 约束],
    列名 列类型[(长度) 约束]
)
```

### 修改表

```sql
# 修改表名
ALTER TABLE 旧表名 RENAME TO 新表名;

# 修改列名
ALTER TABLE 表名 CHANGE COLUMN 旧列名 新列名 新列名类型;

# 修改列的类型或约束
ALTER TABLE 表名 MODIFY COLUMN 列名 列名类型;

# 添加新列
ALTER TABLE 表名 ADD COLUMN 列名 列名类型;

# 删除列
ALTER TABLE 表名 DROP COLUMN 列名;
```

### 删除表

```sql
DROP TABLE [IF EXISTS] 表名;
```

### 复制表

```sql
# 仅复制表的结构
CREATE TABLE 新表 LIKE 旧表;

# 复制表的结构和数据
CREATE TABLE 新表 SELECT * FROM 旧表;
```





## 数据库函数

### 单行函数

> 传入一个参数返回一个值。

#### 字符函数

`LENGTH()`：获取参数的字节个数，单个中文字占3个字节。

`CONCAT()`：拼接字符串。

`UPPER()`、`LOWER()`：大小写转换。

`SUBSTR()`：截取字符串，索引从 1 开始。

`INSTR()`：查询字符所在的索引。

`TRIM()`：去除字符串首尾空格，或者去除首尾指定字符。`TRIM('a' FROM 'aaaXaaa')`

`REPLACE()`：替换字符串。

``IFNULL`：判断参数是否为空。

#### 数学函数

`ROUND()`：四舍五入。

`CEIL()`：向上取整，返回`>=`该参数的最小整数。

`FLOOR()`：向下取整，返回`<=`该参数的最大整数。

`TRUNCATE()`：阶段，小数点后面保留几位。

`MOD()`：取余。

#### 日期函数

`NOW()`：返回系统当前日期 + 时间。

`CURDATE()`：返回系统当前日期。

`CURTIME()`：返回系统当前时间。

`YEAR(NOW())`：获取当前年份。

`MONTH(NOW())`、`MONTHNAME(NOW())`：获取当前月份。

`DAY(NOW())`、`DAYNAME(NOW())`：获取当前日。

`DATEDIFF()`：计算两个日期相差多少天。

`STR_TO_DATE()`：将字符串通过指定格式转换成日期。``STR_TO_DATE('1999-01-20', '%Y-%m-%d')`

`DATE_FORMAT()`：格式化日期。`DATE_FORMAT('1999-01-20', '%Y年%m月%d日')`

#### 其它函数

`VERSION()`：返回数据库版本。

`DATABASE()`：返回当前所在的数据库名称。

`USER()`：返回当前用户。

#### 流程控制函数

`IF()`：if else 效果。`IF(TRUE, '1', '0')`

CASE() 函数：

```sql
# switch case 效果
SELECT 
CASE id
WHEN 1 THEN 11
WHEN 2 THEN 22
WHEN 3 THEN 33
WHEN 4 THEN 44
ELSE `id`
END AS 'new_id'
FROM `person`

# 多重 if 效果
SELECT
CASE
WHEN 1 != 1 THEN 1
WHEN 2 = 2 THEN 2
END
```



### 多行函数（组函数、聚合函数）

> 传入一组参数返回一个值。

#### 数学函数

可以和`DISTINCT`关键字搭配使用。

`SUM()`：求和。

`AVG()`：平均值。

`MAX()`：最大值。

`MIN()`：最小值。

`SUM()`：求和。

`COUNT()`：统计个数。MyISAM 引擎下`COUNT(*)`效率高，InnoDB 引擎下`COUNT(*)`和`COUNT(1)`效率差不多。


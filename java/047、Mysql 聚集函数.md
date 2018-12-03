Mysql 聚集函数有 5 个:

- COUNT() 记录个数
- MAX() 最大值
- MIN() 最小值
- AVG() 平均值
- SUM() 求和

聚集函数常常和分组一起工作.

# GROUP BY 用法解析
`GROUP BY` 语法可以根据给定数据列的每个成员对查询结果进行分组统计, 最终得到一个分组汇总表.

`SELECT` 子句中的列名必须为分组列或列函数 (列函数对于 `GROUP BY` 子句定义的每个组各返回一个结果.).

例子:

某个员工信息表结构和数据如下:

| id | name |  dept  | salary | edlevel |  hiredate  |
|----|------|--------|--------|---------|------------|
|  1 | 张三 | 开发部 |   2000 |       3 | 2009-10-11 |
|  2 | 李四 | 开发部 |   2500 |       3 | 2009-10-01 |
|  3 | 王五 | 设计部 |   2600 |       5 | 2010-10-02 |
|  4 | 王六 | 设计部 |   2300 |       4 | 2010-10-03 |
|  5 | 马七 | 设计部 |   2100 |       4 | 2010-10-06 |
|  6 | 赵八 | 销售部 |   3000 |       5 | 2010-10-05 |
|  7 | 钱九 | 销售部 |   3100 |       7 | 2010-10-07 |
|  8 | 孙十 | 销售部 |   3500 |       7 | 2010-10-06 |


例如, 我想列出每个部门最高薪水的结果, sql 语句如下:

```sql
SELECT DEPT, MAX(SALARY) AS MAXIMUM FROM STAFF GROUP BY DEPT
```

查询结果如下:

|  DEPT  | MAXIMUM |
|--------|---------|
| 开发部 |    2500 |
| 设计部 |    2600 |
| 销售部 |    3500 |

解释一下这个结果:

- 满足 "SELECT 子句中的列名必须为分组列或列函数", 因为 `SELECT` 有 `GROUP BY DEPT` 中包含的列 DEPT.
- "列函数对于 GROUP BY 子句定义的每个组各返回一个结果", 根据部门分组, 对每个部门返回一个结果, 就是每个部门的最高薪水.

> 注意: 计算的是每个部门 (由 `GROUP BY` 子句定义的组) 而不是整个公司的 `MAX(SALARY)`.

例如, 查询每个部门的总的薪水数:

```sql
SELECT DEPT, sum(SALARY) AS total FROM STAFF GROUP BY DEPT
```

查询结果如下:

|  DEPT  | MAXIMUM |
|--------|---------|
| 开发部 |    4500 |
| 设计部 |    7000 |
| 销售部 |    9600 |

# 将 WHERE 子句与 GROUP BY 子句一起使用
分组查询可以在形成组和计算列函数**之前**使用具有消除非限定行的标准 `WHERE` 子句 (必须在 `GROUP BY` 子句之前指定 `WHERE` 子句).

例如, 查询公司 2010 年入职的各个部门每个级别里的最高薪水:

```sql
SELECT DEPT, EDLEVEL, MAX(SALARY) AS MAXIMUM FROM staff WHERE HIREDATE > '2010-01-01' GROUP BY DEPT, EDLEVEL ORDER BY DEPT, EDLEVEL
```

查询结果如下:

|  DEPT  | EDLEVEL | MAXIMUM |
|--------|---------|---------|
| 设计部 |       4 |    2300 |
| 设计部 |       5 |    2600 |
| 销售部 |       5 |    3000 |
| 销售部 |       7 |    3500 |

> 注意: 在 `SELECT` 语句中指定的每个列名也在 `GROUP BY` 子句中提到(未在这两个地方提到的列名将产生错误).

`GROUP BY` 子句对 `DEPT` 和 `EDLEVEL` 的每个**唯一组合**各返回一行.

# 在 GROUP BY 子句之后使用 HAVING 子句
`where` 子句的作用是在对查询结果进行分组前, 将不符合 `where` 条件的行去掉, 即在分组之前过滤数据, 条件中不能包含聚组函数, 使用where条件显示特定的行.

`having` 子句的作用是筛选满足条件的组, 即在分组之后过滤数据, 条件中经常包含聚集函数, 使用 `having` 条件显示特定的组, 也可以使用多个分组标准进行分组.

`having` 子句被限制子已经在SELECT语句中定义的列和聚合表达式上.

例如: 寻找雇员数超过2个的部门的最高和最低薪水:

```sql
SELECT DEPT, MAX(SALARY) AS MAXIMUM, MIN(SALARY) AS MINIMUM FROM staff GROUP BY DEPT HAVING COUNT(*) >2 ORDER BY DEPT
```

查询结果如下:

|  DEPT  | MAXIMUM | MINIMUM |
|--------|---------|---------|
| 设计部 |    2600 |    2100 |
| 销售部 |    3500 |    3000 |

例如: 寻找雇员平均工资大于3000的部门的最高和最低薪水:

```sql
SELECT DEPT, MAX(SALARY) AS MAXIMUM, MIN(SALARY) AS MINIMUM FROM staff GROUP BY DEPT HAVING AVG(SALARY) >3000 ORDER BY DEPT
```

查询结果如下:

|  DEPT  | MAXIMUM | MINIMUM |
|--------|---------|---------|
| 销售部 |    3500 |    3000 |

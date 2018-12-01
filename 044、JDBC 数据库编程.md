# 基础知识补充

**RDBMS**: Relational Database Management System 关系型数据库管理系统

SQL 四种语言: **DDL**、**DML**、**DCL**、**TCL**.

## DDL
Data Definition Language **数据库定义语言** statements are used to define the database structure or schema.

DDL 是 SQL 语言的四大功能之一.

用于定义数据库的三级结构, 包括外模式、概念模式、内模式及其相互之间的映像, 定义数据的完整性、安全控制等约束.

DDL 不需要 commit.

- CREATE
- ALTER
- DROP
- TRUNCATE
- COMMENT
- RENAME

## DML
Data Manipulation Language **数据操纵语言** statements are used for managing data within schema objects.

由 DBMS 提供, 用于让用户或程序员使用, 实现对数据库中数据的操作.

DML 分成交互型 DML 和嵌入型 DML 两类.

依据语言的级别, DML 又可分成过程性 DML 和非过程性 DML 两种.

需要 commit.

- SELECT
- INSERT
- UPDATE
- DELETE
- MERGE
- CALL
- EXPLAIN PLAN
- LOCK TABLE

## DCL
Data Control Language **数据库控制语言**, 授权, 角色控制等

- GRANT 授权
- REVOKE 取消授权

## TCL
Transaction Control Language **事务控制语言**

- SAVEPOINT 设置保存点
- ROLLBACK  回滚
- SET TRANSACTION

# JDBC
## JDBC 配置
**References**:<br>
[通过 JDBC 进行简单的增删改查 (以 MySQL 为例)](http://www.cnblogs.com/wuyuegb2312/p/3872607.html)<br>
[JDBC 快速入门教程](http://www.yiibai.com/jdbc/jdbc_quick_guide.html)

Java Data Base Connectivity 是 Java 语言中用来规范客户端程序如何来访问数据库的应用程序**接口**, 提供了诸如查询和更新数据库中数据的方法, JDBC 也是 Sun Microsystems 的商标, JDBC 是面向关系型数据库的.

> JDBC 相关的 API 是 java.sql 及 javax.sql 下

应用程序通过 JDBC API 与各数据提供的**驱动程序** "交流", 然后数据库驱动程序再与数据库底层 "交流".

![](http://i67.tinypic.com/vdetrk.jpg)

- JDBC 是 Java 访问数据库的应用程序**接口**, 是一套 "规范"
- 驱动程序是各大数据库厂商提供的 JDBC 的具体**实现**

由此可见, 使用 JDBC 操作数据库的第一步就是安装对应的**数据库驱动**.

所谓的**数据库驱动**其实就是一些对应的第三方 JAR 包, 下载下来导入到项目里就可以了 (例如 Mysql 的驱动: mysql-connecter-java).

一般情况下, 我们会在项目目录下新建一个 **lib** 目录用来存放外来的 JAR 包.

![](http://i65.tinypic.com/xfcdck.jpg)

**新建一个 lib 目录**

![](http://i66.tinypic.com/ztg751.jpg)

**把 jar 包拷贝到 lib 目录下后要添加到编译目录** (就是相当于添加到 classpath 路径)

![](http://i64.tinypic.com/5votwn.jpg)

**添加后就会多出一栏引用的库**

![](http://i66.tinypic.com/2j3pedu.jpg)

其余的 JDBC 操作参考上面的 **References** 即可.

## 事务
ACID: 数据库事务正确执行的四个基本要素

- 原子性 (Atomicity)
- 一致性 (Consistency)
- 隔离性 (Isolation)
- 持久性 (Durability)

数据库默认事务是自动提交的, 也就是发一条 sql 它就执行一条, 如果想多条 sql 放在一个事务中执行, 则需要设置 autocommit 为 false, 或者使用 `start transaction` 命令.

参考 wiki: [mysql transaction 与 autocommit](http://wiki.smallcpp.cn/%E6%95%B0%E6%8D%AE%E5%BA%93/mysql%20transaction%20%E4%B8%8E%20autocommit.html)

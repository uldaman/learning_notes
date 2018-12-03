存储过程 (procedure), 类似 mongodb 上的脚本, 就是把要执行的一组 SQL 语句放在 mysql 服务器上, 需要执行这组 SQL 语句时, 只要把参数及调用命令发送给 mysql 服务就可以了, 这样就大大降低了网络传输的损耗.

# MySQL 存储过程的创建
MySQL 存储过程创建的格式:

```sql
CREATE PROCEDURE 过程名 ([过程参数[,...]])
[特性 ...] 过程体
```

例子:

```sql
mysql> DELIMITER //
mysql> CREATE PROCEDURE proc1(OUT s int)
    -> BEGIN
    -> SELECT COUNT(*) INTO s FROM user;
    -> END
    -> //
mysql> DELIMITER;
```

- 这里需要注意的是 `DELIMITER //` 和 `DELIMITER ;` 两句, `DELIMITER` 是分割符的意思, 因为 MySQL 默认以 ";" 为分隔符, 如果我们没有声明分割符, 那么编译器会把存储过程当成 SQL 语句进行处理, 则存储过程的编译过程会报错, 所以要事先用 `DELIMITER` 关键字申明当前段分隔符, 这样 MySQL 才会将 ";" 当做存储过程中的代码, 不会执行这些代码, 用完了之后要把分隔符还原
- 存储过程根据需要可能会有**输入** (IN)、**输出** (OUT)、**输入输出** (INOUT)参数, 这里有一个输出参数 `s`, 类型是 `int` 型, 如果有多个参数用 "," 分割开
- 过程体的开始与结束使用 `BEGIN` 与 `END` 进行标识

存储过程使用起来就像是专门为 Mysql 定义的脚本语言一样 (mongodb 使用 lua 做为脚本语言), 更多使用说明参考: [mysql 存储过程详解](http://blog.sina.com.cn/s/blog_52d20fbf0100ofd5.html)

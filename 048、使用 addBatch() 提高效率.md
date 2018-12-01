**References**:<br>
[JDBC 的 PreparedStatement 启动事务使用批处理 executeBatch()](http://www.cnblogs.com/tommy-huang/p/4540407.html)<br>
[使用 JDBC 的 addBatch() 方法提高效率](http://www.cnblogs.com/husam/p/3830225.html?utm_source=tuicool&utm_medium=referral)

在批量更新 SQL 操作的时候建议使用 addBatch, 这样效率是高些, 数据量越大越能体现出来.

**Statement** 接口里有两个方法:

```java
// 将给定的 SQL 命令添加到此 Statement 对象的当前命令列表中. 通过调用方法 executeBatch 可以批量执行此列表中的命令.
void addBatch(String sql)
// 将一批命令提交给数据库来执行, 如果全部命令执行成功, 则返回更新计数组成的数组.
int[] executeBatch()
```

返回: 包含批中每个命令的一个元素的更新计数所组成的数组 (数组中的每个元素为: 成功处理了命令后, 执行命令所影响数据库中行数的更新计数), 数组的元素根据将命令添加到批中的顺序排序.

**PreparedStatement** 接口里:<br>
重写了 `addBatch()` 的方法, `executeBatch()` 并没有重写.

```java
// 将一组参数添加到此 PreparedStatement 对象的批处理命令中.
void addBatch()
```

因为数据库的处理速度是非常惊人的, 单次吞吐量很大, 执行效率极高, `addBatch()` 把若干 sql 语句装载到一起, 然后一次送到数据库执行, 执行只需要很短的时间.

这里说说 `PreparedStatement` 最重要的 `addbatch()` 的使用.

```java
// 1.建立链接(打电话拨号)
Connection connection = getConnection();

// 2.不自动 Commit (瓜子不是一个一个吃, 全部剥开放桌子上, 然后一口舔了)
connection.setAutoCommit(false);

// 3.预编译SQL语句, 只编译一回哦, 效率高啊.(发明一个剥瓜子的方法, 以后不要总想怎么剥瓜子好, 就这样剥.)
PreparedStatement statement = connection.prepareStatement("INSERT INTO TABLEX VALUES(?, ?)");

// 4.来一个剥一个, 然后放桌子上
// 记录1
statement.setInt(1, 1);
statement.setString(2, "Cujo");
statement.addBatch(); // 把这条执行语句加到 PreparedStatement 对象的批处理命令中

// 记录2
statement.setInt(1, 2);
statement.setString(2, "Fred");
statement.addBatch(); // 把这条执行语句加到 PreparedStatement 对象的批处理命令中

// 记录3
statement.setInt(1, 3);
statement.setString(2, "Mark");
statement.addBatch(); // 把这条执行语句加到 PreparedStatement 对象的批处理命令中

// 批量执行上面3条语句. 一口吞了, 很爽
int[] counts = statement.executeBatch(); // 把以上添加到批处理命令中的所有命令一次过提交给数据库来执行

// Commit it 咽下去, 到肚子(DB)里面
connection.commit();
```

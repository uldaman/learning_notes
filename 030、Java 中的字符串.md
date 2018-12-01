## String 类

在之前的笔记就已经使用了字符串, Java 中, 字符串被作为 String 类型的对象处理, 该类位于 java.lang 包中, 默认情况下这个包被自动导入所有的程序.

创建 String 对象的方法:

```java
String si = "xxx";
String s2 = new String();
String s3 = new String("xxx");
```

String 对象具有__不变性__, 即对象创建后则不能被修改(如果需要一个可以改变的字符串, 我们可以使用 _StringBuffer_ 或者 _StringBuilder_);

而平常见到的 Java 代码中, 对 String 对象的修改其实是__创建了新的对象__.

一些 String 必须了解的细节:

- String s1 = "字符串" 声明了一个字符串对象, s1 存放的是字符串对象的引用, 其实质是创建了新的字符串对象, 变量 s1 指向了新创建的字符串对象.
- 通过 __String s1 = "字符串"__ 方式创建的字符串, 对于多次出现的字符常量, __只会创建一个__, 如 String s1 = "字符串", String s2 = "字符串", 那么 s1、s2 指向的是同一个对象, 即 s1 == s2.
- 通过 __String s1 = new String("字符串")__ 方式创建的字符串, 每次都是产生一个新的对象, 即便两个字符串的内容相同, 使用 "==" 比较时也为 "false", 如果只需比较内容是否相同, 应使用 "__equals()__" 方法(String 类重写了 equals() 方法).


String 类提供了许多用来处理字符串的方法, 例如, 获取字符串长度、对字符串进行截取、将字符串转换为大写或小写、字符串分割等.

![](http://i59.tinypic.com/w1624g.jpg)

小细节:

- 字符串 str 中字符的索引从0开始, 范围为 0 到 str.length()-1
- 使用 indexOf 进行字符或字符串查找时, 如果匹配返回位置索引, 如果没有匹配结果, 返回 -1
- 使用 substring(beginIndex , endIndex) 进行字符串截取时, 包括 beginIndex 位置的字符, 不包括 endIndex 位置的字符

## StringBuilder / StringBuffer
在 Java 中, 除了可以使用 String 类来存储字符串, 还可以使用 __StringBuilder__ 类或 __StringBuffer__ 类存储字符串.

因为对 String 的修改会生成新的 String 对象, 所以当频繁操作 String 时, 就会额外产生很多消耗, 使用 StringBuilder 或 StringBuffer 就可以避免这个问题, 它们是可变类, 任何对它指向的字符串的操作都不会产生新的对象.

至于 StringBuilder 和StringBuffer, 它们基本相似, 不同之处, StringBuffer 是线程安全的, 而 StringBuilder 则没有实现线程安全功能, 所以性能略高.

> 简单的说, 如果用到了多线程就用 StringBuffer, 否则就用 StringBuilder.

StringBuilder str1 = new StringBuilder();

StringBuilder str2 = new StringBuilder(“xxx”);

StringBuilder 类也提供了一些方法来操作字符串:

![](http://i59.tinypic.com/2mzb78k.jpg)

## 字符串<=>基本类型
Java 提供三种方法实现基本类型向字符串转换:

- 使用包装类的 toString() 方法
- 使用 String 类的 valueOf() 方法
- 用一个空字符串加上基本类型, 得到的就是基本类型数据对应的字符串

```java
int c = 10;
String str1 = Integer.toString(c);
String str2 = String.valueOf(c);
String str3 = c + "";
```

将字符串转换成基本类型有两种方法:

- 调用包装类的 parseXxx 静态方法
- 调用包装类的 valueOf() 方法转换为基本类型的包装类, 然后自动拆箱

```java
String str = "10";
int d = Integer.paseInt(str);
int e = Integer.valueOf(str);
```

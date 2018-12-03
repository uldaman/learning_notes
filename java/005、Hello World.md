**步骤**

1. 将代码编写到扩展名为 .java 的文件中.
2. 通过 **javac** 命令对该 java 文件进行编译.
3. 通过 **java** 命令执行生成的 class 文件.

新建一个 txt 文件, 然后改名为 Demo.java, 用记事本打开该文件, 输写代码:

```java
class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello Java");
    }
}
```

DOS下编译:<br>
**javac  xxx.java**, 得到 HelloWorld.class 文件, 称为字节码文件, 注意: 不管 java 源文件是什么名(必须是 .java 结尾), 目标文件都是源文件里面写的 **类名.class**, 但是, 如果源代码为 **public** class HelloWorld, 则源文件名必须和类名一致.

DOS下执行:<br>
**java HelloWorld**, 这里省略扩展名


**代码解析**<bt>
![](http://i60.tinypic.com/314vxu9.jpg)


类是 Java 中的**基本单元**, 可以在类中定义变量和函数, 同时 Java 也规定了, 所有的变量和函数必须存在于类中.

Java 的主函数: **main** (和 C 差不多) 如果没有 main, 执行 class 文件时, 会出现提示: 缺少一个名称为 main 的方法. main 是一个程序的入口, 代码为:<br>
public static void main(String[] args) <br>
此为固定写法.

**注释**<br>
Java 中的注释基本和 C/C++ 类似, 但 java 有个特殊, 在文件开头:
```java
/**
.
.注释内容
.
*/
```

这叫“文本注释”，对程序进行说明后，通过JDK中另一个工具 **javadoc.exe**，将文档注释提取出来形成一个网页——程序说明书。

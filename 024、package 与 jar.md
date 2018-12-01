## 1. 导出 package
包, 即软件包, 可以理解成文件夹.

使用关键字 __package 包名__ 来使用, 如: package test;

```java
// Test.java
package com.test;

class Test {
    // . . .
}
```

编译方法:

```
javac -d . Test.java // 点代表当前目录
```

__-d__ 参数:<br>
该参数的作用是依照包名在指定目录下生成相应的文件夹, 并把生成的目标文件放在该文件夹下.

打包后, 就在当前目录下生成了一个目录树: __./com/test/Test.class__, 同时这个类的命名为 __"包名"＋" . "＋"类名"__, 即: com.test.Test, 也就是说, 使用 java 命令执行时, 要写成:

```
java com.test.Test
```

在非导入的情况下, 调用类的原则如下:(导入的情况见下面一节)

- 如果两个类在同一个包，调用的时候不需要写全名;
- 如果两个类不在一个包，调用的时候需要写全名;

__包命名规范:__

- 所有字母小写
- 域名倒过来写，如：com.baidu

## 2. 导入 package
刚已经知道, 当跨包使用一个类的时候, 要写类的全名，如此太麻烦，可以通过导入包的方法来一次性解决，不再需要写类的全名，就像是在同一个包内一样.

使用 __import__ 关键字，如: import com.test.Test

导入后，在被导入的类中使用导入的类不需要再写全名.

## 3. 权限总结
![权限总结](http://i61.tinypic.com/5p52lf.jpg)

## 4. -cp 参数
当我们实际开发时, 常常会建立 __classes、src、lib__ 三个文件夹来建立文件管理.

- classes, class 目录, 我们编译 \*.java 文件生成的 \*.class 文件都放在这里
- src, 源文件目录, 即我们的 \*.java 文件
- lib, jar 包都放这里

当在 src 文件夹下写好源代码后, 通过 __-d__ 参数指定生成的 \*.class 文件放在 classes 目录下, 通过 __-cp(ClassPath)__ 参数指定依赖库目录, -cp 可以除了指定目录外还可以指定 jar 包.

如, 现在把刚才的 Test.java 放到 src 目录, 然后重新生成 \*.class 文件.

```
javac -cp classes/;lib/xxx.jar; -d classes/ src/Test.java
```

## 5. JAR 包
参考网文: [http://blog.chinaunix.net/uid-692788-id-2681136.html](http://blog.chinaunix.net/uid-692788-id-2681136.html)

JAR 包是 Java 中所特有一种压缩文档, 它是由 JDK ~\bin\jar.exe 命令生成, 该命令将指定目录下的 package 压缩成 jar 包, 其实就可以把它理解为 .zip 包, 当然也是有区别的, JAR 包中有一个 META-INF\MANIFEST.MF 文件, 当你生成 JAR 包时, 它会自动生成.

```
C:\Users\Administrator\Desktop>jar
用法: jar {ctxui}[vfmn0PMe] [jar-file] [manifest-file] [entry-point] [-C dir] files ...
选项:
    -c  创建新档案
    -t  列出档案目录
    -x  从档案中提取指定的 (或所有) 文件
    -u  更新现有档案
    -v  在标准输出中生成详细输出
    -f  指定档案文件名
    -m  包含指定清单文件中的清单信息
    -n  创建新档案后执行 Pack200 规范化
    -e  为捆绑到可执行 jar 文件的独立应用程序指定应用程序入口点
    -0  仅存储; 不使用任何 ZIP 压缩
    -P  保留文件名中的前导 '/' (绝对路径) 和 ".." (父目录) 组件
    -M  不创建条目的清单文件
    -i  为指定的 jar 文件生成索引信息
    -C  更改为指定的目录并包含以下文件
如果任何文件为目录, 则对其进行递归处理。
清单文件名, 档案文件名和入口点名称的指定顺序
与 'm', 'f' 和 'e' 标记的指定顺序相同。

示例 1: 将两个类文件归档到一个名为 classes.jar 的档案中:
       jar cvf classes.jar Foo.class Bar.class
示例 2: 使用现有的清单文件 'mymanifest' 并
           将 foo/ 目录中的所有文件归档到 'classes.jar' 中:
       jar cvfm classes.jar mymanifest -C foo/ .
```

根据上一节所说, 我们的 class 文件都在 classes 目录下, jar 目录生成在 lib 目录下, 所以:

```
jar cvf lib/test.jar -C classes/ . (<=注意这个点, 这个点是必须的, 而且前面有个空格)
```

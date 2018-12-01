完整规范参考: [http://www.hawstein.com/posts/google-java-style.html](http://www.hawstein.com/posts/google-java-style.html)

下面是一些摘要:

##1. 源文件
###1.1 文件名
源文件以其最顶层的类名来命名, 大小写敏感, 文件扩展名为 .java.

##1.2 文件编码
源文件编码格式为 UTF-8

##2. 源文件结构
一个源文件包含(按顺序地):

- 许可证或版权信息(如有需要)
- package 语句
- import 语句
- 一个顶级类(只有一个)

以上每个部分之间用一个空行隔开

###2.1 import 语句
####2.1.1 import不要使用通配符
即, 不要出现类似这样的import语句: **import java.util.***;
####2.1.2 顺序和间距
import 语句可分为以下几组, 按照这个顺序, 每组由一个空行分隔:

- 所有的静态导入独立成组
- com.google imports(仅当这个源文件是在 com.google 包下)
- 第三方的包, 每个顶级包为一组, 字典序, 例如：android, com, junit, org, sun
- java imports
- avax imports

组内不空行, 按字典序排列.

>字典序: 即按英文字母表 a, b, c ... 排序.

##3. 命名约定
###3.1 包名
包名全部小写, 连续的单词用**点**简单地连接起来, 不使用下划线, 如 com.martin.demo.

###3.2 类名
类名都以 **UpperCamelCase**(大写驼峰) 风格编写.

测试类的命名以它要测试的类的名称开始, 以Test结束, 例如, HashTest 或 HashIntegrationTest.

###3.3方法名
方法名都以 **lowerCamelCase**(小写驼峰) 风格编写.

###3.4 常量名
常量名命名模式为 **CONSTANT_CASE**, 全部字母大写, 用下划线分隔单词

###3.5 参数名
参数名以 **lowerCamelCase** 风格编写.

###3.6 变量名
局部变量名以 **lowerCamelCase** 风格编写.

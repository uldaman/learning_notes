#一. 基础概念
在安装 java 之前, 先来熟悉一些概念.

##1. RIA架构
从97年左右开始, 分布式软件分化为 **B/S** 和 **C/S** 这两种泾渭分明的设计架构, B/S 架构的软件的用途主要是一些 Internet 应用, 而 C/S 架构的软件用途则主要是一些企业应用.<br>
而到了2000年以后, 随着 Internet 的普及, B/S 应用软件的发展速度越来越快, 因为 B/S 程序有一些天生优势, 比如无须大规模部署客户端, 导致很多企业应用也纷纷转向采用 B/S 架构.<br>
但传统的 B/S 架构开发是基于页面的、服务器端数据传递的模式, 把网络程序的表示层建立于 HTML 页面之上, 而 HTML 是适合于文本的, 传统的基于页面的系统已经渐渐不能满足网络浏览者的更高的、全方位的体验要求了, 由于客户对体验的需求提高, 导致客户端越来越复杂, 各软件开发商都在想方设法的增加客户端的处理量, 于是 **RIA** 应运而生.<br>
RIA 是集桌面应用程序的最佳用户界面功能与 Web 应用程序的普遍采用和快速、低成本部署以及互动多媒体通信的实时快捷于一体的新一代网络应用程序, 即 **Rich Internet Applications**, 丰富互联网程序, 它是具有高度互动性、丰富用户体验以及功能强大的客户端.

##2. WS 架构
即 web service, 这个其实没什么好讲的, 只是之前我把 **web service** 和 **web server** 弄混了, web server 指的是如 apache 之类的服务端软件, 而 web service 则是 web 应用服务器, 它的作用是为一些 web 应用提供 API, 下面是它的常见 Demo:
![](http://i64.tinypic.com/npf2vp.jpg)<br>
WS 的出现是为了解决**异构系统间的通信**, 它是一个**面向服务的体系结构**.


面向服务的体系结构是一个组件模型, 它将应用程序的不同功能单元(称为服务)通过这些服务之间定义良好的接口和契约联系起来, 接口是采用中立的方式进行定 义的, 它应该独立于实现服务的硬件平台、操作系统和编程语言, 这使得构建在各种各样的系统中的服务可以使用一种统一和通用的方式进行交互.


##3. Java 概念
####3.1 JAVA 版本

- Java EE: 企业版(企业级应用, 如三大框架)
- Java SE: 基础版(我们需要的)
- Java ME: 嵌入版(基本淘汰)

>Java 5.0这三种构架分别叫做：J2SE，J2EE，J2ME

<br>
**JAVA 为什么能跨平台运行？**<br>
JAVA 的字节码文件是被加载到 JAVA 虚拟机 JVM（Java Virtual Machine）中被执行的, 只需操作系统中安装 JVM 就可以执行 Java 程序.
![](http://i65.tinypic.com/15fmxco.jpg)
<br>

####3.2 JAVA 开发环境
![](http://i63.tinypic.com/2qm0kdw.jpg)
<br>

一般的机器上, 只要安装了 JRE 就可以跑起 JAVA 程序(光有 JVM 是不够的).

而开发者则需要安装完整的 JDK 才可以进行 JAVA 开发.

####3.3 JAVA 程序编译过程
![](http://i59.tinypic.com/205wbgk.jpg)

#二. 安装 JDK
访问 oracle 官网 : [http://www.oracle.com/index.html](http://www.oracle.com/index.html)

![](http://i65.tinypic.com/11ryjc5.jpg)

![](http://i64.tinypic.com/504f34.jpg)

![](http://i64.tinypic.com/2vcth8j.jpg)


<br>
下载下来直接安装即可, 安装的时候, 不要勾选 **公共 JRE**, 因为 JDK 中已包含一个专用的 JRE.<br>**注意**: 路径中不要有中文或特殊符号、空格等.
![](http://i65.tinypic.com/14v1xxi.jpg)
<br>

安装好 JDK 后, 在指定的安装目录里有很多的文件和文件夹, 其中 **bin** 目录和 **lib** 目录需要额外注意下, bin 文件夹里保存的是 java 的可执行文件, 常用的是 java.exe 和 javac.exe(具体用处后面再说), lib 里保存的是 java 的类库, 和 C++ 开发中的 lib 文件差不多的意思.

<br>
安装完后, 测试下是否安装成功.<br>
呼出 CMD 窗口, 执行 javac, 会发现报错.<br>
这是因为, 在 DOS 中执行某程序, 如果执行的程序在**当前目录**下不存在, 系统会在 **Path 这个环境变量指定的目录**中查找, 如果还没找到, 就会错误提示 ‘Javac’ 不是内部或外部命令, 也不是可运行的程序或批处理文件, 所以, 要想运行 ‘Javac’, 则必须 每次进入 JDK 安装路径\bin 或修改 Path 环境变量, 为了一劳永逸,  我们来修改系统环境变量.

> 不仅 DOS 下是这样, winodws 下执行程序, 程序会先在当前目录下查找依赖文件, 如果没找到, 就去 path 指定的目录下查找.


**配置环境变量**

- JAVA_HOME: 配置 JDK 安装路径, 我这里是 D:\jdk1.8.0_73
- PATH: 配置 JDK 命令文件路径(bin目录), %JAVA_HOME%\bin
- CLASSPATH: 见后面详细说明

此时, 重启 DOS 窗口, 再次执行 javac 命令, 就可以看到效果了.

#三. CLASSPATH
CLASSPATH 之所以要单独拿出来讲, 是因为以后出现的莫名其妙的怪问题 80% 以上都可能是由于 CLASSPATH 设置不对引起的, 所以要加倍小心才行.


设置 CLASSPATH 的目的, 在于告诉 Java 执行环境, 在哪些目录下可以找到所要执行的 Java 程序所需要的类或者包, 如果不设置 CLASSPATH, 那么默认就会到当前工作目录寻找.


CLASSPATH=.;%java_home%\lib\tools.jar;%java_home%\lib\dt.jar;%java_home%\lib\rt.jar;%JAVA_HOME%\lib


首先要注意的是最前面的".;" —— 句点分号, 这个是告诉JDK, 搜索 CLASS 时先查找当前目录, 然后是 tools.jar、dt.jar 和 rt.jar 三个 jar 包的路径, 最后是 lib 目录的路径.


> 除了配置环境变量外,  执行 java 或者 javac 命令时, 可以跟上 -classpath 参数来直接指定类目录.

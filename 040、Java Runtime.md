Reference: [深入研究 java.lang.Runtime 类](http://lavasoft.blog.51cto.com/62575/15565/)

# 一、概述
Runtime 类封装了运行时的环境, 每个 Java 应用程序都有一个 Runtime 类实例, 使应用程序能够与其运行的环境相连接.

一般不能实例化一个 Runtime 对象, 应用程序也不能创建自己的 Runtime 类实例, 但可以通过 getRuntime 方法获取当前 Runtime 运行时对象的引用.

一旦得到了一个当前的 Runtime 对象的引用, 就可以调用 Runtime 对象的方法去控制 Java 虚拟机的状态和行为.

# 二、API预览
- `addShutdownHook(Thread hook)`
    + 注册新的虚拟机来关闭挂钩
- `availableProcessors()`
    + 向 Java 虚拟机返回可用处理器的数目
- `exec(String command)`
    + 在单独的进程中执行指定的字符串命令
- `exec(String[] cmdarray)`
    + 在单独的进程中执行指定命令和变量
- `exec(String[] cmdarray, String[] envp)`
    + 在指定环境的独立进程中执行指定命令和变量
- `exec(String[] cmdarray, String[] envp, File dir)`
    + 在指定环境和工作目录的独立进程中执行指定的命令和变量
- `exec(String command, String[] envp)`
    + 在指定环境的单独进程中执行指定的字符串命令
- `exec(String command, String[] envp, File dir)`
    + 在有指定环境和工作目录的独立进程中执行指定的字符串命令
- `exit(int status)`
    + 通过启动虚拟机的关闭序列, 终止当前正在运行的 Java 虚拟机
- `freeMemory()`
    + 返回 Java 虚拟机中的空闲内存量
- `gc()`
    + 运行垃圾回收器
- `InputStream getLocalizedInputStream(InputStream in)`
    + 已过时. 从 JDK 1.1 开始, 将本地编码字节流转换为 Unicode 字符流的首选方法是使用 InputStreamReader 和 BufferedReader 类
- `OutputStream getLocalizedOutputStream(OutputStream out)`
    + 已过时. 从 JDK 1.1 开始, 将 Unicode 字符流转换为本地编码字节流的首选方法是使用 OutputStreamWriter、BufferedWriter 和 PrintWriter 类
- `getRuntime()`
    + 返回与当前 Java 应用程序相关的运行时对象
- `halt(int status)`
    + 强行终止目前正在运行的 Java 虚拟机
- `load(String filename)`
    + 加载作为动态库的指定文件名
- `loadLibrary(String libname)`
    + 加载具有指定库名的动态库
- `maxMemory()`
    + 返回 Java 虚拟机试图使用的最大内存量
- `removeShutdownHook(Thread hook)`
    + 取消注册某个先前已注册的虚拟机关闭挂钩
- `runFinalization()`
    + 运行挂起 finalization 的所有对象的终止方法
- `runFinalizersOnExit(value)`
    + 已过时. 此方法本身具有不安全性. 它可能对正在使用的对象调用终结方法, 而其他线程正在操作这些对象, - 从而导致不正确的行为或死锁
- `totalMemory()`
    + 返回 Java 虚拟机中的内存总量
- `traceInstructions(on)`
    + 启用／禁用指令跟踪
- `traceMethodCalls(on)`
    + 启用／禁用方法调用跟踪

#三、常见的应用
## 1、内存管理
Java 提供了无用单元自动收集机制, 通过 `totalMemory()`和 `freeMemory()` 方法可以知道对象的堆内存有多大, 还剩多少.

Java 会周期性的回收垃圾对象 (未使用的对象), 以便释放内存空间. 但是如果想先于收集器的下一次指定周期来收集废弃的对象, 可以通过调用 `gc()` 方法来根据需要运行无用单元收集器.

一个很好的试验方法是先调用 `gc()` 方法, 然后调用 `freeMemory()` 方法来查看基本的内存使用情况, 接着执行代码, 然后再次调用 `freeMemory()` 方法看看分配了多少内存.

下面的程序演示了这个构想. // 此实例来自《java 核心技术》卷一

```java
class MemoryDemo {
    public static void main(String args[]) {
        Runtime r = Runtime.getRuntime();
        long mem1,mem2;
        Integer someints[] = new Integer[1000];
        System.out.println("Total memory is ：" + r.totalMemory());
        mem1 = r.freeMemory();
        System.out.println("Initial free is : " + mem1);
        r.gc();
        mem1 = r.freeMemory();
        System.out.println("Free memory after garbage collection : " + mem1);
        //allocate integers
        for(int i=0; i<1000; i++) someints[i] = new Integer(i);
        mem2 = r.freeMemory();
        System.out.println("Free memory after allocation : " + mem2);
        System.out.println("Memory used by allocation : " +(mem1-mem2));
        //discard Intergers
        for(int i=0; i<1000; i++) someints[i] = null;
        r.gc(); //request garbage collection
        mem2 = r.freeMemory();
        System.out.println("Free memory after collecting " + "discarded integers : " + mem2);
    }
}
```

编译后运行结果如下 (不同的机器不同时间运行的结果也不一定一样):

```
Total memory is ：2031616
Initial free is : 1818488
Free memory after garbage collection : 1888808
Free memory after allocation : 1872224
Memory used by allocation : 16584
Free memory after collecting discarded integers : 1888808
```

## 2、执行其他程序
在安全的环境中, 可以在多任务操作系统中使用 Java 去执行其他特别大的进程 (也就是程序).

`exec()` 方法有几种形式命名想要运行的程序和它的输入参数.

`exec()` 方法返回一个 Process 对象, 可以使用这个对象控制 Java 程序与新运行的进程进行交互.

`exec()` 方法本质是依赖于环境.

下面的例子是使用 `exec()` 方法启动 windows 的记事本 notepad. / /此实例来自《Java 核心技术》卷一

```java
class ExecDemo {
    public static void main(String args[]) {
        Runtime r = Runtime.getRuntime();
        Process p = null;
        try {
            p = r.exec("notepad");
        } catch (Exception e) {
            System.out.println("Error executing notepad.");
        }
    }
}
```

`exec()` 还有其他几种形式, 例子中演示的是最常用的一种.

`exec()` 方法返回 Process 对象后, 在新程序开始运行后就可以使用 Process 的方法了.

可以用 `destory()` 方法杀死子进程, 也可以使用 `waitFor()` 方法等待程序直到子程序结束, `exitValue()` 方法返回子进程结束时返回的值, 如果没有错误, 将返回 0, 否则返回非 0.

下面是关于 `exec()``方法的例子的改进版本. 例子被修改为等待, 直到运行的进程退出: // 此实例来自《Java 核心技术》卷一

```java
class ExecDemoFini {
    public static void main(String args[]) {
    Runtime r = Runtime.getRuntime();
    Process p = null;
    try{
        p = r.exec("notepad");
        p.waitFor();
    } catch (Exception e) {
        System.out.println("Error executing notepad.");
    }
        System.out.println("Notepad returned " + p.exitValue());
    }
}
```

下面是运行的结果 (当关闭记事本后, 会接着运行程序, 打印信息):

```
Notepad returned 0
请按任意键继续. . .
```

当子进程正在运行时, 可以对标准输入输出进行读写, `getOutputStream()` 方法和 `getInPutStream()` 方法返回对子进程的标准输入和输出.

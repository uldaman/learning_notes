References:<br>
[\[Java\] Socket 和 ServerSocket 学习笔记](http://www.cnblogs.com/rond/p/3565113.html)<br>
[Java Socket 编程](http://haohaoxuexi.iteye.com/blog/1979837)

# TCP VS UDP
**UDP:**

- 每个数据报中都给出了完整的地址信息, 因此无需建立发送方和接收方的连接
- UDP 传输数据时是有**大小限制**的, 每个被传输的数据报必须限定在 64KB 之内
- UDP 是一个**不可靠**的协议, 发送方所发送的数据报并不一定以相同的次序到达接收方

**TCP:**

- 面向连接的协议, 在 socket 之间进行数据传输之前**必然要建立连接**, 所以在 TCP 中需要连接时间
- TCP 传输数据大小限制, 一旦连接建立起来, 双方的 socket 就可以按统一的格式传输大的数据
- TCP 是一个**可靠**的协议, 它确保接收方完全正确地获取发送方所发送的全部数据.

**应用:**

- TCP 在网络通信上有极强的生命力, 例如远程连接 (Telnet) 和文件传输 (FTP) 都需要不定长度的数据被可靠地传输; 但是可靠的传输是要付出代价的, 对数据内容正确性的检验必然占用计算机的处理时间和网络的带宽, 因此 TCP 传输的效率不如 UDP 高
- UDP 操作简单, 而且仅需要较少的监护, 因此通常用于局域网高可靠性的分散系统中 client/server 应用程序; 例如视频会议系统, 并不要求音频视频数据绝对的正确, 只要保证连贯性就可以了, 这种情况下显然使用 UDP 会更合理一些

# Java Socket
在 Java 环境下, Socket 主要是指基于 TCP/IP 协议的网络编程 (UDP 用的是另一套).

在包 java.net 中提供了两个类 `Socket` 和 `ServerSocket`, 分别用来表示双向连接的客户端和服务端, 这是两个封装得非常好的类, 使用很方便, 其**构造方法**如下:

```java
Socket(InetAddress address, int port);
Socket(InetAddress address, int port, boolean stream);
Socket(String host, int prot);
Socket(String host, int prot, boolean stream);
Socket(SocketImpl impl)
Socket(String host, int port, InetAddress localAddr, int localPort)
Socket(InetAddress address, int port, InetAddress localAddr, int localPort)

ServerSocket(int port);
ServerSocket(int port, int backlog);
ServerSocket(int port, int backlog, InetAddress bindAddr)
```

其中:

- `address`、`host` 和 `port` 分别是双向连接中另一方的 IP 地址、主机名和端 口号
- `stream `指明 socket 是流 socket 还是数据报 socket
- `localPort` 表示本地主机的端口号, `localAddr` 和 `bindAddr` 是本地机器的地址 (ServerSocket 的主机地址)
- `impl` 是 socket 的父类, 既可以用来创建 ServerSocket 又可 以用来创建 Socket
- `count` 则表示服务端所能支持的最大连接数

注意, 在选择端口时, 必须小心, 每一个端口提供一种特定的服务, 只有给出正确的端口, 才 能获得相应的服务.

0~1023 的端口号为系统所保留, 例如 http 服务的端口号为 80, telnet 服务的端口号为 21, ftp 服务的端口号为 23, 所以在选择端口号时, 最好选择一个大于 1023 的数以防止发生冲突.

在创建 socket 时如果发生错误, 将产生 IOException, 在程序中必须对之作出处理, 所以在创建 Socket 或 ServerSocket 是必须捕获或抛出例外.

**Socket 方法**

```java
getInetAddress();   //远程服务端的 IP 地址
getPort();          //远程服务端的端口
getLocalAddress();  //本地客户端的 IP 地址
getLocalPort();     //本地客户端的端口
getInputStream();   //获得输入流
getOutStream();     //获得输出流
```

值得注意的是, 在这些方法里面, 最重要的就是 `getInputStream()` 和 `getOutputStream()`了.

**Socket 状态**

```java
isClosed();     //连接是否已关闭, 若关闭, 返回 true；否则返回 false
isConnect();    //如果曾经连接过, 返回 true；否则返回 false
isBound();      //如果 Socket 已经与本地一个端口绑定, 返回 true；否则返回 false
```

如果要确认 Socket 的状态是否处于连接中, 下面语句是很好的判断方式:

```java
boolean isConnection=socket.isConnected() && !socket.isClosed();   //判断当前是否处于连接
```

# 代码片段
客户端

```java
Socket socket = new Socket("127.0.0.1", 9999);  //创建 Socket 指定 ip 地址和端口号
InputStream is = socket.getInputStream();   //获取输入流
OutputStream os = socket.getOutputStream(); //获取输出流
BufferedReader br = new BufferedReader(new InputStreamReader(is));
PrintStream ps = new PrintStream(os);

System.out.println(br.readLine());
ps.println("我想报名就业班");
System.out.println(br.readLine());
ps.println("爷不学了");
socket.close();
```

服务端

```java
ServerSocket server = new ServerSocket(9999);   //创建服务器
Socket socket = server.accept();            //接受客户端的请求
InputStream is = socket.getInputStream();   //获取输入流
OutputStream os = socket.getOutputStream(); //获取输出流

BufferedReader br = new BufferedReader(new InputStreamReader(is));
PrintStream ps = new PrintStream(os);

ps.println("欢迎咨询 Java 培训班");
System.out.println(br.readLine());
ps.println("班满了, 请报下一期吧");
System.out.println(br.readLine());
server.close();
socket.close();
```

多线程版服务端

```java
ServerSocket server = new ServerSocket(9999);   //创建服务器
    while(true) {
        final Socket socket = server.accept();  //接受客户端的请求
        new Thread() {
            public void run() {
                try {
                    BufferedReader br = new BufferedReader(new InputStreamReader(socket.getInputStream()));
                    PrintStream ps = new PrintStream(socket.getOutputStream());
                    ps.println("欢迎咨询 Java 培训班");
                    System.out.println(br.readLine());
                    ps.println("班满了, 请报下一期吧");
                    System.out.println(br.readLine());
                    socket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }.start();
    }
}
```



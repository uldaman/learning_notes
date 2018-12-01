Reference: [TCP/IP 详解](http://blog.csdn.net/goodboy1881/article/category/204448)

# TCP/IP
TCP/IP 协议 (Transfer Controln Protocol / Internet Protocol) 叫做传输控制/网际协议, 又叫网络通讯协议, 这个协议是 Internet 国际互联网络的基础.

什么是协议?

在世界上各地, 各种各样的电脑运行着各自不同的操作系统为大家服务, 这些电脑在表达同一种信息的时候所使用的方法是千差万别. 就好像圣经中上帝打乱了各地人的口音, 让他们无法合作一样.

而协议, 说白了就是定义计算机间通信的通用"语言", 大家都按这种协议来通信.

TCP/IP 是网络中使用的基本的通信协议. 虽然从名字上看 TCP/IP 包括两个协议, 传输控制协议 (TCP) 和网际协议 (IP), 但 TCP/IP 实际上是**一组协议** (协议簇), 它包括上百个各种功能的协议: TCP、IP、UDP、ICMP、RIP、TELNETFTP、SMTP、ARP、TFTP 等许多协议, 这些协议一起称为 TCP/IP 协议. 以下是对协议族中一些常用协议英文名称和用途作一介绍:

- TCP (Transport Control Protocol) 传输控制协议
- IP (Internetworking Protocol) 网间网协议
- UDP (User Datagram Protocol) 用户数据报协议
- ICMP (Internet Control Message Protocol) 互联网控制信息协议
- SMTP (Simple Mail Transfer Protocol) 简单邮件传输协议
- SNMP (Simple Network manage Protocol) 简单网络管理协议
- FTP (File Transfer Protocol) 文件传输协议
- ARP (Address Resolation Protocol) 地址解析协议

# OSI
最早网络刚刚出现的时候, 很多大型的公司都拥有了网络技术, 公司内部计算机可以相互连接, 可以却不能与其它公司连接, 因为没有一个统一的规范. 计算机之间相互传输的信息对方不能理解, 所以不能互联.

ISO 为了更好的使网络应用更为普及, 就推出了 OSI 参考模型 (Open System Interconnection) 开放系统互联, 其含义就是推荐所有公司使用这个规范来控制网络, 这样所有公司都有相同的规范, 就能互联了.

- **物理层** \-\-\- 数据表示, 物理层规定了激活、维持、关闭通信端点之间的机械特性、电气特性、功能特性以及过程特性, 该层为上层协议提供了一个传输数据的物理媒体, EIA/TIA、RS-232、EIA/TIA RS\-449、V.35、RJ\-45 等
- **数据链路层** \-\-\- 主机间通信, 数据链路层在不可靠的物理介质上提供可靠的传输, SDLC、HDLC、PPP、STP、帧中继等
- 网络层 \-\-\- 端到端的连接, 网络层负责对子网间的数据包进行路由选择, 此外, 网络层还可以实现拥塞控制、网际互连等功能. IP、IPX、RIP、OSPF 等
- **传输层** \-\-\- 寻址和最短路径, 传输层是第一个端到端, 即主机到主机的层次, 传输层负责将上层数据分段并提供端到端的、可靠的或不可靠的传输, TCP、UDP、SPX 等
- **会话层** \-\-\- 介质访问, 会话层管理主机之间的会话进程, 即负责建立、管理、终止进程之间的会话, 会话层还利用在数据中插入校验点来实现数据的同步, NetBIOS、ZIP (AppleTalk 区域信息协议) 等
- **表示层** \-\-\- 二进制传输, 表示层对上层数据或信息进行变换以保证一个主机应用层信息可以被另一个主机的应用程序理解, 表示层的数据转换包括数据的加密、压缩、格式转换等, ASCII、ASN.1、JPEG、MPEG等
- **应用层** \-\-\- 应用层为操作系统或网络应用程序提供访问网络服务的接口, Telnet、FTP、HTTP、SNMP 等

# OSI 与 TCP/IP
首先 TCP/IP 他是一个**协议簇**; 而 OSI (开放系统互联) 则是一个**模型**.

TCP/IP 是由一些交互性的模块做成的分层次的协议, 其中每个模块提供特定的功能; OSI 则指定了哪个功能是属于哪一层的.

TCP/IP 协议被组织成四个概念层.

![](http://i65.tinypic.com/fk6yqq.jpg)

| OSI 中的层 |                  功能                  |               TCP/IP 协议族              |
|------------|----------------------------------------|------------------------------------------|
| 应用层     | 文件传输, 电子邮件, 文件服务, 虚拟终端 | TFTP, HTTP, SNMP, FTP, SMTP, DNS, Telnet |
| 表示层     | 数据格式化, 代码转换, 数据加密         | 没有协议                                 |
| 会话层     | 解除或建立与别的接点的联系             | 没有协议                                 |
| 传输层     | 提供端对端的接口                       | TCP, UDP                                 |
| 网络层     | 为数据包选择路由                       | IP, ICMP, RIP, OSPF, BGP, IGMP           |
| 数据链路层 | 传输有地址的帧以及错误检测功能         | SLIP, CSLIP, PPP, ARP, RARP, MTU         |
| 物理层     | 以二进制数据形式在物理媒体上传输数据   | ISO2110, IEEE802, IEEE802.2              |

# 数据报传输的过程
数据发送时, 由上层向下层封装.

四层, 协议层传输的是数据报文, 主要是协议格式;<br>
三层, 网络层传输的是数据包, 包含数据报文, 并且增加传输使用的IP地址等三层信息;<br>
二层, 数据链路层传输的是数据帧, 包含数据包, 并且增加相应MAC地址与二层信息.

数据接收的时候, 下层向上层解封装.

**IP 数据报**:

![](http://i67.tinypic.com/5ldlsn.jpg)

**TCP 数据报**:

![](http://i66.tinypic.com/dzf5m9.jpg)

**文件传输应用例子**:

![](http://i65.tinypic.com/5caaty.jpg)

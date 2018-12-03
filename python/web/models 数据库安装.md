这里使用 MySql, 先去官网下载安装.
为方便, 已经提前下载好转到 云盘 了: [http://yunpan.cn/cm6WWhDRLwKYf](http://yunpan.cn/cm6WWhDRLwKYf) 访问密码 a3c1

![](http://i62.tinypic.com/141mlwy.jpg)

![](http://i59.tinypic.com/1z3apzq.jpg)

![](http://i60.tinypic.com/28qrm69.jpg)

接下来是一些配置选项:
![](http://i58.tinypic.com/28h2brd.jpg)

![](http://i59.tinypic.com/2uny2ra.jpg)

![](http://i61.tinypic.com/2cmptgy.jpg)



接下来, 把安装路径下的 Bin 目录添加到环境变量, 如: C:\Program Files\MySQL\MySQL Server 5.6\bin;

**然后设置字符集**

进入安装目录, 如: C:\Program Files\MySQL\MySQL Server 5.6

拷贝一份 my-default.ini, 并命名为 my.ini, 这就是我们的配置文件

打开 my.ini, 添加下面两行:
[mysql]
default_character_set=utf8

[mysqld]
character_set_server=utf8

然后, 重启服务, 打开 CMD, 输入命令: mysql -uroot -p, 链接 MySql 后, 再输入命令: \s, 查看字符集.
![](http://i58.tinypic.com/202gig.jpg)
??

#DOS 命令

| 命令| 说明|
|:-------------|:-------------|
|dir|directory 查看当前目录|
|x /?|查看任意命令的帮助, 有些命令的帮助要用 x --help|
|tab 键|自动补全|
|shift + tab 键|反向操作 tab|
|cd|进入目录(要先进入盘符, 进入方法为直接输 盘符:)|
|.|当前目录|
|..|上级目录, 如 cd .. 进入上级目录|
|mkdir/md|创建目录|
|touch|创建文件 (这个命令有点问题, 有的电脑上没有)|
|echo|重定向流, 如 echo xxx > a.txt, 将 xxx 写入 a.txt 里(覆盖), echo yyy >> a.txt, 将 yyy 追加到 a.txt 里|
|type|查看文件内容|
|rmdir|删除目录|
|del|删除文件/目录|
|copy|拷贝|
|move|移动|
|cls|清屏|
|start|启动程序, /affinity 指定程序的 cpu 亲和力, 注意是 16 进制(怎么算出 cpu 亲和力的 16 进制见下面的 windows 优化)|
|ipconfig|查看主机 ip 信息|
|ping|查看网络连通情况|
|telnet|测试端口连通情况, telnet 公网ip 端口|
|set|查看环境变量|
|exit|退出 DOS|

>小贴士:
目录和路径的区别: 目录其实是我们平时口中的文件夹, 而路径才是指文件的地址.

#win 快捷键
| 快捷键| 说明|
|:-------------|:-------------|
|文本编辑||
|ctrl + c|复制|
|ctrl + x|剪切|
|ctrl + v|粘贴|
|ctrl + s|保存|
|ctrl + a|全选|
|ctrl + z|撤销|
|ctrl + y|重做|
|系统功能||
|win + r|运行|
|services.msc|服务|
|notepad|记事本|
|calc|计算器|
|mspaint|画板|
|regedit|注册表|
|ctrl + shift + esc|任务管理器|
|win + d|显示桌面|
|ctrl + e|打开 我的电脑|
|ctrl + n|新建|

>小贴士:
在资源管理器的路径栏里直接输 cmd, 可在当前路径打开 cmd.


#windows 优化

### cpu 亲和力
首先通过 cpu-z 查看下自己电脑是几核的, 因为 Intel 有个超线程技术, 可以在一个内核上模拟出两个内核, 所以现在有的电脑是双核四线程, 或者四核八线程, 在任务管理器->性能里看到的是线程数而不是内核数.<br>
winodws 的多任务机制本质是时间片切换, 设置程序的 cpu 亲和力, 就是指定某个程序由哪一个或者哪几个内核执行, 而不去占用其他内核的时间片.<br>
当某个程序的需要的资源非常大时, 用多个内核轮转时间片也无济于事, 结果就是让整个系统挂掉, 所以此时, 我们可以设置这个程序只由某一个或者某几个内核去执行, 这样就空出来了其他的内核, 让系统不至于挂掉.

![](http://i63.tinypic.com/286wq2v.jpg)


![](http://i67.tinypic.com/dptfdf.jpg)

上面的 dos 命令里有提到, 通过 **start /affinity 0x?** 可以以指定的 cpu 亲和力启动某个程序, 现在来看看怎么算出这个亲和力.<br>
假设我们的 cpu 是四核 cpu, 分别是 1~4 号核心, 每个核心通过二进制的 0/1 来控制是否启用该核心(0 为关闭, 1 为启用).<br>
例如现在要设定程序的执行 cpu 是 1 号核心和 2 号核心, 那么对应的二进制就是 1100, 但是, 这个结果是反的, 1 号 cpu 占的应该是个数位, 2 号 cpu 占的是十数位, 以此类推, 所以这个结果就是 0011, 转换成 16 进制就是 0x3.

> 一般情况下, 我们都会写一个批处理文件来启动程序.

### 禁用主题
右键我的电脑->属性->高级系统设置->高级->性能[设置]->调整为最佳性能->勾选`平滑屏幕字体边缘`->确定.

### 其他优化
将以下代码copy到记事本中存为bat文件并以管理员身份运行运行
```
@echo off
sc stop MpsSvc **停止windows firewall服务**
sc stop WinDefend **停止windows defender服务**
sc stop TabletInputService **停止tablet pc服务**
sc config TabletInputService start= DISABLED **禁止tablet pc服务**
sc config MpsSvc start= DISABLED **禁止windows firewall服务**
sc config WinDefend start= DEMAND **windows defender服务改为手动**
REG ADD HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v ConsentPromptBehaviorAdmin /t REG_DWORD /d 0 /f
REG ADD HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v EnableLUA /t REG_DWORD /d 0 /f
REG ADD HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v PromptOnSecureDesktop /t REG_DWORD /d 0 /f
echo.& pause
```
<br>
将以下代码copy到记事本中存为reg文件运行
```
Windows Registry Editor Version 5.00
[HKEY_CURRENT_USER\Control Panel\Mouse]
"MouseHoverTime"="100"
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurePipeServers\winreg]
"RemoteRegAccess"=dword:00000001
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced]
"DesktopLivePreviewHoverTime"=dword:000000f4
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Sidebar\Settings]
"AllowElevatedProcess"=dword:00000001
```


- 运行gpedit.msc：计算机配置-管理模板-系统-关机选项-关闭会阻止或取消关机......：启动此功能
- 控制面板>操作中心>更改操作中心设置:将所有钩选的选项全部去掉并将下方客户体验改善计划关闭
- 控制面板>程序和功能>左侧的“打开或关闭windows功能”:
- 关闭“远程差分压缩”
- 关闭“tablet pc组件”（有触摸屏/画图板的用户不执行）
- 关闭“游戏”（需要玩windows内置小游戏的用户不执行）
- 关闭“打印和文件服务”（需要使用打印机的用户不执行）
- 控制面板>声音>“通信”选项卡:钩选不执行任何操作
- 控制面板>鼠标>“指针选项”选项卡:取消“提高指针精度”的钩选
- 控制面板>通知区域图标:钩选下方“始终在任务栏上显示所有图标和通知”
- 控制面板>通知区域图标>打开或关闭系统图标:关闭“操作中心”
- 控制面板>自动播放:将“为所有媒体和设备使用自动播放”的钩选取消
- 计算机属性>远程设置:关闭远程协助
- 计算机属性>系统保护>配置:关闭系统还原并删除还原点
- 计算机属性>高级系统设置>设置(性能)>高级选项卡>更改(虚拟内存):自定义大小，更改后只有按“设置”按钮才能生效【内存 2G（x86）/3G（x64）及以上设为200MB,内存1.5G（x86）/2G（x64）者设为512MB,内存1G（x86）/2G（x64）者 设为1024MB.内存小于等于1G（x86）/1.5G（x64）者进入控制面板>管理工具>服务:禁用superfetch服务】(虚拟 内存最大最小值设成一样是关键。另外，如果提示内存小了可以依据个人情况调大)

重启计算机(会自动重启两次)


# 进制转换
N进制 －－> 十进制<br>
N进制数abcdef(这是一个6位数)<br>
转成10进制 －－>a\*N^5+b\*N^4+c\*N^3+d\*N^2+e\*N^1+f\*N^0<br>

十进制 －－> N进制<br>
10除以N的余数倒序排列得到的就是N进制

![](http://i68.tinypic.com/adi00m.jpg)


每四位二进制可换算成十六进制, 这个可以背下来.


|二进制|十六进制|
|:--------:|:--------:|
|0000|0|
|0001|1|
|0010|2|
|0011|3|
|0100|4|
|0101|5|
|0110|6|
|0111|7|
|1000|8|
|1001|9|
|1010|A|
|1011|B|
|1100|C|
|1101|D|
|1110|E|
|1111|F|

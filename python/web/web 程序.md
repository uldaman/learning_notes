MVC -- M 是指业务模型, V 是指用户界面, C 则是控制器.

MVC 模式同时提供了对 HTML、CSS 和 JavaScript 的完全控制.

Model（模型）是应用程序中用于处理应用程序数据逻辑的部分, 通常模型对象负责在数据库中存取数据.

View（视图）是应用程序中处理数据显示的部分, 通常视图是依据模型数据创建的(简单说就是前端).

Controller（控制器）是应用程序中处理用户交互的部分, 通常控制器负责从视图读取数据, 控制用户输入, 并向模型发送数据.

因为 "C" 是 Django 框架本身处理而导致 Django 大部分精彩的东西在于模型, 模板和视图
所以 Django 被称位 MTV 框架:
M, 代表模型, 是数据访问层, 它包含了关于数据的一切东西, 怎样得到数据, 怎样验证数据, 它具有什么行为以及数据之间的关系.
T, 代表模板, 是展现层, 它包含了呈现相关的决策, 如内容怎样在Web页面中显示以及其它类型的文档.
V, 代表视图, 是业务逻辑层, 它包含了访问模型的逻辑和选择合适的模板.
你可以认为视图是模型和模板的桥梁, 如果对 MVC 框架熟悉, 可以把 Django 的 view 想象成 "controllers" ,
把 Django 的 template 想象成 "views" , 这是对 MVC 的不同解释造成的不幸的混乱.

**Django 的安装**

使用** **`pip install Django 就能直接安装, 当然也可以下载安装包, 安装好后, 进入 Python 环境:


    <span style="color: #0000ff">import</span><span style="color: #000000"> django
    django.VERSION</span>

就能知道有没有安装成功了...`



**创建 Django 程序**




使用命令行创建




先进入目标文件夹, 如桌面:
![](http://i57.tinypic.com/zkki11.jpg)




然后使用 **django-admin startproject** **项目名** 来新建项目(前提是 django-admin.exe 已经被安装到 Python Scripts 目录下, 并且 Scripts 目录被添加到环境变量).
![](http://i60.tinypic.com/ifn22u.jpg)




该项目自带一个简单的单请求 web server, 使用 **python manage.py runserver** 就能运行起这个 web server, 当然这个 web server 不能应用于生产环境, 还需要我们修改.
**runserver** 后还能跟端口做为参数;
它还有一个常用的参数 **--noreload**, 它表示如果服务运行时, 改变了 .py 文件, 服务不会重新加载(只能重启服务), 如果不加这个参数, 那么服务会重新加载 .py 文件. 这个参数主要是用来供** WingIDE 调试 Django **程序的(在后面还有说明).




打开 WingIDE, 在右侧添加刚新建的项目:
![](http://i61.tinypic.com/slmx4x.jpg)




接下来, 菜单栏会多出 Django 一项, 点击选择 [Configure Project for Django].![](http://i61.tinypic.com/29efwjb.jpg)




![](http://i58.tinypic.com/2wehxzt.jpg)




调试时, 点击运行, 然后填写命令行参数就能调试 Django 程序了.![](http://i62.tinypic.com/i2m06s.jpg)




但是这样, 会存在一个问题, 就是调试时, WingIDE 会启动一个新的 Ptyhon 进程来做为服务器, 当我们停止调试时, 并不能关掉这个新创建的进程, 加上 **--noreload** 能解决这个问题, 这个参数将父进程直接作为 Http Server 来运行.
)0_

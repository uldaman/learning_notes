manage.py -- 启动、入口文件

settings.py -- 配置文件

urls.py -- 路由系统( url 映射)

wsgi.py -- wsgi 模块 (Web Server Gateway Interface 服务器网关接口)

开始创建的那个项目是一个工程项目, 一个项目里会包含多个 app, 每一个 app 才是一个网站.
在 WingIDE 的 Django 菜单里可以创建 app:
![](http://oi61.tinypic.com/i78itc.jpg)

好, 现在创建了一个名为 web 的 app, 它的框架如下:
![](http://oi58.tinypic.com/2crozf8.jpg)

migrations 文件夹暂时不管.
admin.py 是 Django 提供的一个后台管理, 先不管.
models.py 是 MTV 里 M (数据库操作).
tests.py 是 Django 提供的测试, 先不管.
views.py 是 MTV 里的 V (业务逻辑).
MTV 里的 T 需要自己去建立...在 web 上右键创建一个 template 文件夹即可.

manage.py 除了 runserver 外, 还有几个其他常用命令:
manage.py startapp appname -- 新建 app
manage.py syncdb -- 同步数据库
manage.py makemigrations -- 配合上 syncdb
manage.py migrate -- 配合上 syncdb

最简单案例:

打开 web 里的 views.py, 定义 index 方法:


    <span style="color: #0000ff">from</span> django.http.response <span style="color: #0000ff">import</span><span style="color: #000000"> HttpResponse

    </span><span style="color: #008000">#</span><span style="color: #008000"> Create your views here.</span>
    <span style="color: #0000ff">def</span><span style="color: #000000"> index(request):
        </span><span style="color: #0000ff">return</span> HttpResponse(<span style="color: #800000">'</span><span style="color: #800000">index</span><span style="color: #800000">'</span>)


打开 工程 里的 urls.py, 定义 url 映射:



    <span style="color: #0000ff">from</span> web.views <span style="color: #0000ff">import</span><span style="color: #000000"> index
    urlpatterns </span>=<span style="color: #000000"> [
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^admin/</span><span style="color: #800000">'</span><span style="color: #000000">, include(admin.site.urls)),
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^index/</span><span style="color: #800000">'</span><span style="color: #000000">, index),
    ]</span>


现在启动服务, 在浏览器里输入: [http://127.0.0.1:8000/index](http://127.0.0.1:8000/index) 就能看到效果了.注意, 如果不自定义 urlpatterns , 那么 [http://127.0.0.1:8000](http://127.0.0.1:8000) 会返回一个默认的页面, 如果定义了 urlpatterns 就不会返回默认页面.
egc

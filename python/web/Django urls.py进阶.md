原 urls.py:

    <span style="color: #0000ff">from</span> views <span style="color: #0000ff">import</span><span style="color: #000000"> Page

    urlpatterns </span>=<span style="color: #000000"> [
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^page/(?P<index>\d*)/$</span><span style="color: #800000">'</span><span style="color: #000000">, Page),
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^page/$</span><span style="color: #800000">'</span>, Page, {<span style="color: #800000">'</span><span style="color: #800000">index</span><span style="color: #800000">'</span>:<span style="color: #800000">'</span><span style="color: #800000">1</span><span style="color: #800000">'</span><span style="color: #000000">}),
    ]</span>







更改后:




    <span style="color: #0000ff">from</span> django.conf.urls <span style="color: #0000ff">import</span><span style="color: #000000"> patterns

    urlpatterns </span>= patterns(<span style="color: #800000">'</span><span style="color: #800000">web.views</span><span style="color: #800000">'</span><span style="color: #000000">,
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^page/(?P<index>\d*)/$</span><span style="color: #800000">'</span>, <span style="color: #800000">'</span><span style="color: #800000">Page</span><span style="color: #800000">'</span><span style="color: #000000">),
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^page/$</span><span style="color: #800000">'</span>, <span style="color: #800000">'</span><span style="color: #800000">Page</span><span style="color: #800000">'</span>, {<span style="color: #800000">'</span><span style="color: #800000">index</span><span style="color: #800000">'</span>:<span style="color: #800000">'</span><span style="color: #800000">1</span><span style="color: #800000">'</span>}), <span style="color: #008000">#</span><span style="color: #008000">注意这里需要使用单引号</span>
    )




可以看到, 引入了 patterns, 并且不再需要 from view import Page, 当需要导入众多函数时, 这将非常有用.




    <span style="color: #0000ff">from</span> django.conf.urls <span style="color: #0000ff">import</span><span style="color: #000000"> include, url, patterns
    </span><span style="color: #0000ff">from</span> django.contrib <span style="color: #0000ff">import</span><span style="color: #000000"> admin

    urlpatterns </span>= patterns(<span style="color: #800000">'</span><span style="color: #800000">web.views</span><span style="color: #800000">'</span><span style="color: #000000">,
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^page/(?P<index>\d*)/$</span><span style="color: #800000">'</span>, <span style="color: #800000">'</span><span style="color: #800000">Page</span><span style="color: #800000">'</span><span style="color: #000000">),
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^page/$</span><span style="color: #800000">'</span>, <span style="color: #800000">'</span><span style="color: #800000">Page</span><span style="color: #800000">'</span>, {<span style="color: #800000">'</span><span style="color: #800000">index</span><span style="color: #800000">'</span>:<span style="color: #800000">'</span><span style="color: #800000">1</span><span style="color: #800000">'</span><span style="color: #000000">}),
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^add/(?P<name>\d*)</span><span style="color: #800000">'</span>, <span style="color: #800000">'</span><span style="color: #800000">Add</span><span style="color: #800000">'</span><span style="color: #000000">),
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^dele/(?P<id>\d*)</span><span style="color: #800000">'</span>, <span style="color: #800000">'</span><span style="color: #800000">Delete</span><span style="color: #800000">'</span><span style="color: #000000">),
    ......
    )</span>




不用写一长串的 from views import Page, Add, Delete, Update, All, Login, Regist, DoAjax, Search







* * *






#### 在 [路由系统](http://www.smallcpp.cn/small_892.php) 这一章中, 介绍了 urls.py 的其他应用.




如,** url 转发**, **正则命名组**, **附带参数** 等.
nle

**restful**

一种软件架构风格, 是设计风格而不是标准, 只提供了一组设计原则和约束条件.

可参考:
[廖雪峰 编写 API](http://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/001402402473485d8c205b735ee4e698f90769960fcec4b000)
[http://www.ruanyifeng.com/blog/2011/09/restful](http://www.ruanyifeng.com/blog/2011/09/restful)
[http://www.ruanyifeng.com/blog/2014/05/restful_api.html](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)

使用 restful 有利于客服端与服务器的交互, Django 中提供了很多插件实现 restful, 这里, 我们使用 Rest_Framework 插件.

**Rest_Framework**

安装: pip install djangorestframework

在 Django 中添加:


    <span style="color: #008000">#</span><span style="color: #008000"> settings.py</span>
    INSTALLED_APPS =<span style="color: #000000"> (
        ......
        </span><span style="color: #800000">'</span><span style="color: #800000">rest_framework</span><span style="color: #800000">'</span><span style="color: #000000">,
    )</span>

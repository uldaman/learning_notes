<span style="color: #0000ff">from</span> web.views <span style="color: #0000ff">import</span><span style="color: #000000"> index

    urlpatterns </span>=<span style="color: #000000"> [
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^admin/</span><span style="color: #800000">'</span><span style="color: #000000">, include(admin.site.urls)),
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^index/</span><span style="color: #800000">'</span><span style="color: #000000">, index),
    ]</span>




第一个元素是模式匹配字符串(正则表达式).




* * *


这里考虑一种情况, 想像下那种论坛上帖子的页数, 如果每个 Url 都要做 映射, 那该怎么做?
![](http://i57.tinypic.com/14aa5c6.jpg)


所以 Django 提供了动态配置 URL 的功能, 即用正则表达式.



    <span style="color: #0000ff">from</span> web.views <span style="color: #0000ff">import</span><span style="color: #000000"> Page

    urlpatterns </span>=<span style="color: #000000"> [
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^admin/</span><span style="color: #800000">'</span><span style="color: #000000">, include(admin.site.urls)),
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^page/(\d*)</span><span style="color: #800000">'</span><span style="color: #000000">, Page),
    ]</span>


如果使用了这种方式, 那么其映射函数就要对后面的(\d*)做处理.



    <span style="color: #0000ff">from</span> django.http.response <span style="color: #0000ff">import</span><span style="color: #000000"> HttpResponse

    </span><span style="color: #008000">#</span><span style="color: #008000"> Create your views here.</span>
    <span style="color: #0000ff">def</span><span style="color: #000000"> Page(request, id):
        format </span>= <span style="color: #800000">"</span><span style="color: #800000">Page-%s</span><span style="color: #800000">"</span><span style="color: #000000">
        format </span>= format %<span style="color: #000000"> id
        </span><span style="color: #0000ff">return</span> HttpResponse(format)





可以看到, 这个函数比普通映射函数, 多了一个参数.
这个参数和正则表达式上是一致的, 如果正则是 r(^page/(\d*)/(\d*)), 那么它的映射函数比上面的函数还要多一个参数.




如果要想在正则里指定映射函数的参数名, 可以使用**命名组**的方式:



    url(r<span style="color: #800000">'</span><span style="color: #800000">^page/(?P<index>\d*)</span><span style="color: #800000">'</span>, Page),

这样, 它的映射函数的第二个参数就必须命名为 index.



另外, 还可以**设计默认值**:



    url(r<span style="color: #800000">'</span><span style="color: #800000">^page/(?P<index>\d*)/$</span><span style="color: #800000">'</span><span style="color: #000000">, Page),
    url(r</span><span style="color: #800000">'</span><span style="color: #800000">^page/$</span><span style="color: #800000">'</span>, Page, {<span style="color: #800000">'</span><span style="color: #800000">index</span><span style="color: #800000">'</span>:<span style="color: #800000">'</span><span style="color: #800000">1</span><span style="color: #800000">'</span>}),

当访问, http://localhost:8000/page/ 2 时, 它会匹配到第一条规则, 而当访问 http://localhost:8000/page/ 时, 第一条规则因为加了 **$ 限定符,** 所以不能被匹配, 它会匹配到第二条规则, 而第二条规则指定了 index 的值 为 1, 这样页面就会访问到 http://localhost:8000/page/ 1





    url(r<span style="color: #800000">'</span><span style="color: #800000">^page/(?P<index>\d*)</span><span style="color: #800000">'</span>, Page, {<span style="color: #800000">"</span><span style="color: #800000">index</span><span style="color: #800000">"</span> : 1}),




**需要注意一下, 当命名组变量和默认变量重名时, 默认变量将生效, 而命名组变量将无意义.**







* * *







以上方法, 会让一个工程下的所有 app 都通过 urls.py, 如果 app 多的话, 会让逻辑容易出错, 所以还可以让每个 app 的 url 映射来处理.





首先, 要改造 urls.py 文件:



    urlpatterns =<span style="color: #000000"> [
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^admin/</span><span style="color: #800000">'</span><span style="color: #000000">, include(admin.site.urls)),
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^web/</span><span style="color: #800000">'</span>, include(<span style="color: #800000">"</span><span style="color: #800000">web.urls</span><span style="color: #800000">"</span><span style="color: #000000">)),
    ]</span>





代码逻辑很清晰, 不多解释了...




然后, 在 web 项目里新建一个 urls.py 文件:



    <span style="color: #0000ff">from</span> django.conf.urls <span style="color: #0000ff">import</span><span style="color: #000000"> include, url
    </span><span style="color: #0000ff">from</span> django.contrib <span style="color: #0000ff">import</span><span style="color: #000000"> admin

    </span><span style="color: #0000ff">from</span> views <span style="color: #0000ff">import</span><span style="color: #000000"> Page

    urlpatterns </span>=<span style="color: #000000"> [
    </span><span style="color: #000000">    url(r</span><span style="color: #800000">'</span><span style="color: #800000">^page/(?P<index>\d*)</span><span style="color: #800000">'</span>, Page, {<span style="color: #800000">"</span><span style="color: #800000">index</span><span style="color: #800000">"</span> : 1<span style="color: #000000">}),
    ]</span>






Django 中的路由系统和其他语言的框架有所不同, 在 Django 中每一个请求的 url 都要有一条路由映射, 这样才能将请求交给对一个的 view 中的函数去处理;
而其他大部分的 Web 框架则是对一类的 url 请求做一条路由映射, 从而是路由系统变得简洁.
通过反射机制, 也可以为 Django 开发一套动态的路由系统;
Demo: [http://yunpan.cn/cm6gUbnMiqhfq](http://yunpan.cn/cm6gUbnMiqhfq) 访问密码 5808
>nf

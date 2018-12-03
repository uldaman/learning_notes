**通用视图**

有的时候, views.py 的处理只是简单的返回一个 html, 这种情况就可以通过 [通用视图] 简写在 urls.py 中.

    <span style="color: #008000">#</span><span style="color: #008000">from django.views.generic.simlpe import direct_to_template 版本更新, 用下面这个代替</span>
    <span style="color: #0000ff">from</span> django.views.generic.base <span style="color: #0000ff">import</span><span style="color: #000000"> TemplateView

    urlpatterns </span>=<span style="color: #000000"> [
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^about/$</span><span style="color: #800000">'</span>, TemplateView.as_view(template_name=<span style="color: #800000">'</span><span style="color: #800000">about.html</span><span style="color: #800000">'</span><span style="color: #000000">)),
    ]</span>




以上是通用视图的最基本应用, 通用视图还可以用来渲染模板.
它的优势常常体现在对数据库的访问上.
参考文章: [http://www.pythontip.com/blog/post/12172/](http://www.pythontip.com/blog/post/12172/)







* * *






有的时候, views.py 里的视图函数有部分相同的逻辑, 此时可以把这部分逻辑提取出来.




views.py




    <span style="color: #0000ff">def</span><span style="color: #000000"> view1(request):
        </span><span style="color: #0000ff">return</span> render_to_response(<span style="color: #800000">'</span><span style="color: #800000">1.html</span><span style="color: #800000">'</span><span style="color: #000000">)

    </span><span style="color: #0000ff">def</span><span style="color: #000000"> view2(request):
        </span><span style="color: #0000ff">return</span> render_to_response(<span style="color: #800000">'</span><span style="color: #800000">2.html</span><span style="color: #800000">'</span><span style="color: #000000">)

    </span><span style="color: #0000ff">def</span><span style="color: #000000"> views(func):
        </span><span style="color: #0000ff">def</span><span style="color: #000000"> view(request):
            </span><span style="color: #008000">#</span><span style="color: #008000"> do something</span>
            <span style="color: #0000ff">return</span><span style="color: #000000"> func(request)
        </span><span style="color: #0000ff">return</span> view





urls.py




    urlpatterns =<span style="color: #000000"> [
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^view1/$</span><span style="color: #800000">'</span><span style="color: #000000">, views(view1)),
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^view2/$</span><span style="color: #800000">'</span><span style="color: #000000">, views(view2)),
    ]</span>
00"

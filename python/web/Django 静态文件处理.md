**第一种**

**适用于 static 目录在 app 目录下**的情况.

在 app 目录下新建一个 static 目录(如果不存在), 把 js 等有静态文件放在这下面;
然后检查下 settings.py 中是否有这一条: **STATIC_URL = '/static/'. **如果不存在 那就手动加上;
接下来在 HTML 里编写代码:


    <span style="color: #0000ff"><</span><span style="color: #800000">script </span><span style="color: #ff0000">src</span><span style="color: #0000ff">="/static/jquery-1.11.3.js"</span><span style="color: #0000ff">></</span><span style="color: #800000">script</span><span style="color: #0000ff">></span>





注意 src 是用 / 开头的, 这样访问的就是绝对路径.




具体步骤:
1. 在 app 目录下创建 static 目录, 将静态文件和相关文件夹放到此目录下
2. 确保 settings.py 中的 INSTALLED_APPS 中包含 django.contrib.staticfiles
3. 设置 settings.py 中的 STATIC_URL 的值为“/static/”
4. 在 HTML 中使用 /static/ 做为静态文件的前缀, 如 <img src=”/static/img/logo.png”>




特别注意: 按照上述设置, settings.py 中的 DEBUG 选项必须设置为 True, 否则 '/static/' 无法映射到静态文件目录.


**第二种**




**适用于 static 目录在总工程目录下**的情况 (总工程目录指的是和 manage.py 一个目录).




这种情况, 要在 settings.py 里的 **STATIC_URL **下新增一项:



    STATICFILES_DIRS =<span style="color: #000000"> (
            os.path.join(BASE_DIR,</span><span style="color: #800000">'</span><span style="color: #800000">static</span><span style="color: #800000">'</span><span style="color: #000000">),
        )</span>





其它操作同步第一种情况.




* * *





下面是备份资料, 实战时 {{ STATIC_URL }} 不能被替换, 不懂为什么...




在 HTML 中使用 **{{ STATIC_URL }}** 作为静态文件路径前缀, 如: <img src=”{{ STATIC_URL }}jQuery-1.11.js”>





最后通过 render_to_response 返回页面时, 要修改成 RequestContext 渲染模式, 该模式会附带 STATIC_URL.



    <span style="color: #008000">#</span><span style="color: #008000">-*- coding:utf-8 -*-</span>
    <span style="color: #0000ff">from</span> django.template <span style="color: #0000ff">import</span><span style="color: #000000"> RequestContext

    </span><span style="color: #0000ff">def</span><span style="color: #000000"> DoAjax(request):
        </span><span style="color: #008000">#</span><span style="color: #008000">print request.method</span>
        <span style="color: #0000ff">if</span> request.method == <span style="color: #800000">'</span><span style="color: #800000">POST</span><span style="color: #800000">'</span><span style="color: #000000">:
            </span><span style="color: #0000ff">return</span> HttpResponse(<span style="color: #800000">'</span><span style="color: #800000">OK</span><span style="color: #800000">'</span><span style="color: #000000">)
        </span><span style="color: #0000ff">return</span> render_to_response(<span style="color: #800000">'</span><span style="color: #800000">ajax.html</span><span style="color: #800000">'</span>, {}, context_instance=RequestContext(request))






具体步骤:
1. 在工程目录下创建 static 目录(manage.py 所在目录), 将静态文件和相关文件夹放到此目录下
2. 确保 settings.py 中的 INSTALLED_APPS 中包含 django.contrib.staticfiles
3. 设置 settings.py 中的 STATIC_URL 的值为“/static/”
4. 在 settings.py 中添加:



    STATICFILES_DIRS =<span style="color: #000000"> (
            os.path.join(BASE_DIR,</span><span style="color: #800000">'</span><span style="color: #800000">static</span><span style="color: #800000">'</span><span style="color: #000000">),
    )</span>


5. 在模版中使用 {{ STATIC_URL }} 作为静态文件路径前缀, 如: <img src=”{{ STATIC_URL }}img/logo.png”>
6. 渲染模版要换成 RequestContext, 否则模版中无法引用到 STATIC_URL 对应的值.



特别注意: 按照上述设置, settings.py 中的 DEBUG 选项必须设置为 True, 否则 '/static/' 无法映射到静态文件目录.
s0R

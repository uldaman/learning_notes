session 是用来保持会话的机制.

它在 Django 中是被保存在 DB 里, 一般是名为 django_session 的表, 所以它是全局的, 那么它就可以用来保存登录用户的详细信息.

设置 : request.session[‘key’] = value (可以是字典)

获取 : request.session.get(‘key’, None) 第二个参数是默认值

销毁 : del request.session[‘key’]

登录成功后, 保存 session, 然后重定向(redirect)登录后的页面, 当然在登录后的页面里还要验证 session.

接下来, 就是什么时候销毁 session(不可能永远保持登录...), session 有默认的过期时间, Django 是两周, 可以在 settings.py 里的 **SESSION_COOKIE_AGE** 可设置, 单位秒.
还可以设置浏览器关闭时自动销毁, **SESSION_EXPIRE_AT_BROWSER_CLOSE** = True.
当然也可以通过 del request.session[‘key’] 来主动销毁.

上代码:


    <span style="color: #0000ff">from</span> django.shortcuts <span style="color: #0000ff">import</span><span style="color: #000000"> redirect
    </span><span style="color: #0000ff"></span>

    <span style="color: #0000ff">def</span><span style="color: #000000"> All(request, p):
        loginDict </span>= request.session.get(<span style="color: #800000">'</span><span style="color: #800000">is_login</span><span style="color: #800000">'</span><span style="color: #000000">, None)
        </span><span style="color: #0000ff">if</span><span style="color: #000000"> loginDict :
            currentPage </span>=<span style="color: #000000"> int(p)
            start </span>= 5 * (currentPage - 1<span style="color: #000000">)
            end </span>= 5 *<span style="color: #000000"> currentPage
            itemList </span>=<span style="color: #000000"> Asset.objects.all()[start : end]

            nMaxCount </span>=<span style="color: #000000"> Asset.objects.all().count()
            nPages </span>= (nMaxCount + 5 -1)// 5<span style="color: #000000">
            pageList </span>=<span style="color: #000000"> []


            </span><span style="color: #0000ff">if</span> currentPage > 1<span style="color: #000000">:
                pre_herf </span>= <span style="color: #800000">'</span><span style="color: #800000"><a href="/web/all/%d">上一页</a></span><span style="color: #800000">'</span> % (currentPage - 1<span style="color: #000000">)
                pageList.append(mark_safe(pre_herf))

            </span><span style="color: #0000ff">for</span> x <span style="color: #0000ff">in</span> range(1,nPages + 1<span style="color: #000000">):
                herf </span>= <span style="color: #800000">'</span><span style="color: #800000"><a href="/web/all/%d">%d</a></span><span style="color: #800000">'</span> %<span style="color: #000000"> (x, x)
                pageList.append(mark_safe(herf))

            </span><span style="color: #0000ff">if</span> currentPage <<span style="color: #000000"> nPages:
                next_herf </span>= <span style="color: #800000">'</span><span style="color: #800000"><a href="/web/all/%d">下一页</a></span><span style="color: #800000">'</span> % (currentPage + 1<span style="color: #000000">)
                pageList.append(mark_safe(next_herf))

            result </span>= render_to_response(<span style="color: #800000">'</span><span style="color: #800000">index.html</span><span style="color: #800000">'</span><span style="color: #000000">,
                                        {</span><span style="color: #800000">'</span><span style="color: #800000">data</span><span style="color: #800000">'</span><span style="color: #000000">:itemList,
                                         </span><span style="color: #800000">'</span><span style="color: #800000">count</span><span style="color: #800000">'</span><span style="color: #000000">:nMaxCount,
                                         </span><span style="color: #800000">'</span><span style="color: #800000">pages</span><span style="color: #800000">'</span><span style="color: #000000">:nPages,
                                         </span><span style="color: #800000">'</span><span style="color: #800000">pageList</span><span style="color: #800000">'</span><span style="color: #000000">:pageList,
                                         </span><span style="color: #800000">'</span><span style="color: #800000">user</span><span style="color: #800000">'</span>:loginDict[<span style="color: #800000">'</span><span style="color: #800000">user</span><span style="color: #800000">'</span><span style="color: #000000">]})
            </span><span style="color: #0000ff">return</span><span style="color: #000000"> result
        </span><span style="color: #0000ff">return</span> redirect(<span style="color: #800000">'</span><span style="color: #800000">/web/login</span><span style="color: #800000">'</span><span style="color: #000000">)

    </span><span style="color: #0000ff">def</span><span style="color: #000000"> Login(request):
        </span><span style="color: #008000">#</span><span style="color: #008000">print request.method</span>
        <span style="color: #0000ff">if</span> request.method == <span style="color: #800000">'</span><span style="color: #800000">POST</span><span style="color: #800000">'</span><span style="color: #000000">:
            fname </span>= request.POST.get(<span style="color: #800000">'</span><span style="color: #800000">fname</span><span style="color: #800000">'</span>, None) <span style="color: #008000">#</span><span style="color: #008000"> 第二个参数是默认返回值</span>
            lname = request.POST.get(<span style="color: #800000">'</span><span style="color: #800000">lname</span><span style="color: #800000">'</span><span style="color: #000000">, None)
            result </span>= UserInfo.objects.filter(username=fname, password=<span style="color: #000000">lname).count()
            </span><span style="color: #0000ff">if</span> result == 1<span style="color: #000000">:
                </span><span style="color: #008000">#</span><span style="color: #008000">return HttpResponse('登录成功')</span>
                request.session[<span style="color: #800000">'</span><span style="color: #800000">is_login</span><span style="color: #800000">'</span>] = {<span style="color: #800000">'</span><span style="color: #800000">user</span><span style="color: #800000">'</span><span style="color: #000000">:fname}
                </span><span style="color: #0000ff">return</span> redirect(<span style="color: #800000">'</span><span style="color: #800000">/web/all</span><span style="color: #800000">'</span>) <span style="color: #008000">#</span><span style="color: #008000"> 这个可以是 完整的URL 或者 绝对路径</span>
            <span style="color: #0000ff">else</span><span style="color: #000000">:
                </span><span style="color: #0000ff">return</span> render_to_response(<span style="color: #800000">'</span><span style="color: #800000">login.html</span><span style="color: #800000">'</span>, {<span style="color: #800000">'</span><span style="color: #800000">status</span><span style="color: #800000">'</span>:<span style="color: #800000">'</span><span style="color: #800000">用户名或密码错误</span><span style="color: #800000">'</span><span style="color: #000000">})

        </span><span style="color: #0000ff">return</span> render_to_response(<span style="color: #800000">'</span><span style="color: #800000">login.html</span><span style="color: #800000">'</span>)





    <span style="color: #0000ff"><!</span><span style="color: #ff00ff">DOCTYPE html</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"><</span><span style="color: #800000">html</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"><</span><span style="color: #800000">head </span><span style="color: #ff0000">lang</span><span style="color: #0000ff">="en"</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">meta </span><span style="color: #ff0000">charset</span><span style="color: #0000ff">="UTF-8"</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">title</span><span style="color: #0000ff">></span>你好<span style="color: #0000ff"></</span><span style="color: #800000">title</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"></</span><span style="color: #800000">head</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"><</span><span style="color: #800000">body</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">div</span><span style="color: #0000ff">></span><span style="color: #000000">
            用户: {{user}}
        </span><span style="color: #0000ff"></</span><span style="color: #800000">div</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">div</span><span style="color: #0000ff">></span>
            <span style="color: #0000ff"><</span><span style="color: #800000">table </span><span style="color: #ff0000">border</span><span style="color: #0000ff">="1px"</span><span style="color: #0000ff">></span><span style="color: #000000">
                {% for item in data %}
                    </span><span style="color: #0000ff"><</span><span style="color: #800000">tr</span><span style="color: #0000ff">></span>
                        <span style="color: #0000ff"><</span><span style="color: #800000">td</span><span style="color: #0000ff">></span>{{item.id}}<span style="color: #0000ff"></</span><span style="color: #800000">td</span><span style="color: #0000ff">></span>
                        <span style="color: #0000ff"><</span><span style="color: #800000">td</span><span style="color: #0000ff">></span>{{item.hostName}}<span style="color: #0000ff"></</span><span style="color: #800000">td</span><span style="color: #0000ff">></span>
                    <span style="color: #0000ff"></</span><span style="color: #800000">tr</span><span style="color: #0000ff">></span><span style="color: #000000">
                {% endfor %}
            </span><span style="color: #0000ff"></</span><span style="color: #800000">table</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"></</span><span style="color: #800000">div</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">div</span><span style="color: #0000ff">></span><span style="color: #000000">
            总条数: {{count}}
        </span><span style="color: #0000ff"></</span><span style="color: #800000">div</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">div</span><span style="color: #0000ff">></span><span style="color: #000000">
            总页: {{pages}}
            </span><span style="color: #0000ff"><</span><span style="color: #800000">br</span><span style="color: #0000ff">/></span><span style="color: #000000">
            {% for item in pageList %}
                </span><span style="color: #0000ff"><</span><span style="color: #800000">span</span><span style="color: #0000ff">></span>{{item}}<span style="color: #0000ff"></</span><span style="color: #800000">span</span><span style="color: #0000ff">></span><span style="color: #000000">
            {% endfor %}
        </span><span style="color: #0000ff"></</span><span style="color: #800000">div</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"></</span><span style="color: #800000">body</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"></</span><span style="color: #800000">html</span><span style="color: #0000ff">></span>








* * *


**Django 中 cookies 和 session 的关系**




只要是 HTTP 协议, 就会有 Cookie 这个东西; 只要你的浏览器没有禁用 Cookie, 就会使用它, 是不分用什么语言、用什么框架的, 在下次向服务器提交或者浏览的时候就会把上次保存下来的 Cookie 带上发送向服务器;
BS 结构理论上是没有同步的服务器-客户端的状态维持的, 所以Cookie本质上就是一种异步的状态维护,所有这一切浏览器都帮我们搞定了, 所以不用关心.
Django 中的 session 依赖于Cookie, 如果浏览器不支持 Cookie 的话, Django 的 Session 也就无从用起了.




session 和 cookie 的关联关系:
![](http://i3.tietuku.com/6e19e67b6459d582.png)




每个用户过来都会生成一个以 cookies 为 key 的 session, 而这个它 的 value 又可以是一个字典, 可以用来保存这个用户的相关信息, 如登录名、IP等等.
3 t

**模板语言**

依赖于 Python, 可以把数据内嵌到 html 中展示.

eg:


    <span style="color: #0000ff"><!</span><span style="color: #ff00ff">DOCTYPE html</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"><</span><span style="color: #800000">html</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"><</span><span style="color: #800000">head </span><span style="color: #ff0000">lang</span><span style="color: #0000ff">="en"</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">meta </span><span style="color: #ff0000">charset</span><span style="color: #0000ff">="UTF-8"</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">title</span><span style="color: #0000ff">></span>你好<span style="color: #0000ff"></</span><span style="color: #800000">title</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"></</span><span style="color: #800000">head</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"><</span><span style="color: #800000">body</span><span style="color: #0000ff">></span>
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
    <span style="color: #0000ff"></</span><span style="color: #800000">body</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"></</span><span style="color: #800000">html</span><span style="color: #0000ff">></span>









    <span style="color: #008000">#</span><span style="color: #008000"> settings.py</span>
    TEMPLATE_DIRS =<span style="color: #000000"> (
        os.path.join(BASE_DIR, </span><span style="color: #800000">'</span><span style="color: #800000">web/template</span><span style="color: #800000">'</span><span style="color: #000000">),
    )

    TEMPLATES </span>=<span style="color: #000000"> [
        {
            </span><span style="color: #800000">'</span><span style="color: #800000">BACKEND</span><span style="color: #800000">'</span>: <span style="color: #800000">'</span><span style="color: #800000">django.template.backends.django.DjangoTemplates</span><span style="color: #800000">'</span><span style="color: #000000">,
            </span><span style="color: #800000">'</span><span style="color: #800000">DIRS</span><span style="color: #800000">'</span>: TEMPLATE_DIRS, <span style="color: #008000">#</span><span style="color: #008000"> 这里本身是[ ], 需要改成 TEMPLATE_DIRS</span>
            <span style="color: #800000">'</span><span style="color: #800000">APP_DIRS</span><span style="color: #800000">'</span><span style="color: #000000">: True,
            </span><span style="color: #800000">'</span><span style="color: #800000">OPTIONS</span><span style="color: #800000">'</span><span style="color: #000000">: {
                </span><span style="color: #800000">'</span><span style="color: #800000">context_processors</span><span style="color: #800000">'</span><span style="color: #000000">: [
                    </span><span style="color: #800000">'</span><span style="color: #800000">django.template.context_processors.debug</span><span style="color: #800000">'</span><span style="color: #000000">,
                    </span><span style="color: #800000">'</span><span style="color: #800000">django.template.context_processors.request</span><span style="color: #800000">'</span><span style="color: #000000">,
                    </span><span style="color: #800000">'</span><span style="color: #800000">django.contrib.auth.context_processors.auth</span><span style="color: #800000">'</span><span style="color: #000000">,
                    </span><span style="color: #800000">'</span><span style="color: #800000">django.contrib.messages.context_processors.messages</span><span style="color: #800000">'</span><span style="color: #000000">,
                ],
            },
        },
    ]</span>









    <span style="color: #008000">#</span><span style="color: #008000"> views.py in web</span>
    <span style="color: #0000ff">def</span><span style="color: #000000"> All(request):
        itemList </span>=<span style="color: #000000"> Asset.objects.all()
        result </span>= render_to_response(<span style="color: #800000">'</span><span style="color: #800000">index.html</span><span style="color: #800000">'</span>, {<span style="color: #800000">'</span><span style="color: #800000">data</span><span style="color: #800000">'</span><span style="color: #000000">:itemList})
        </span><span style="color: #0000ff">return</span> result


if html 模板



    <span style="color: #000000">{% if xx %}
        true
    {% else %}
        假
    {% endif %}</span>





需要注意, 没有 elseif 这种语法, 如果想要实现, 就需要在 else 里再使用 if 模板.




    <span style="color: #000000">{% ifequal xx 'yy' %}
        true 如果 xx == yy
    {% else %}
        假
    {% endif %｝</span>


模板还有一些帮助方法, 可以快速实现某些功能, 如首字母大写等.



帮助方法：

```
{{ xxx|date:"Y-m-d H:i:s"}}
{{ xxx|truncatewords:"30" }}
{{ xxx|first|upper }}
{{ xxx|lower }}
```

具体可自行搜索, 另外还可以通过 **simple_tag** 来自定义帮助方法:




1. 在 app 中创建 templatetags 包




2. 创建任意 .py 文件，如：xx.py







    <span style="color: #008000">#</span><span style="color: #008000">!/usr/bin/env python</span><span style="color: #008000">
    #</span><span style="color: #008000">coding:utf-8</span>
    <span style="color: #0000ff">from</span> django <span style="color: #0000ff">import</span><span style="color: #000000"> template
    </span><span style="color: #0000ff">from</span> django.template.base <span style="color: #0000ff">import</span><span style="color: #000000"> resolve_variable, Node, TemplateSyntaxError

    register </span>=<span style="color: #000000"> template.Library()

    @register.simple_tag
    </span><span style="color: #0000ff">def</span><span style="color: #000000"> my_simple_time(v1,v2,v3):
        </span><span style="color: #0000ff">return</span>  v1 + v2 +<span style="color: #000000"> v3</span>


3. 在使用自定义 simple_tag 的 html 文件中导入之前创建的 xx.py 文件名(不用带后缀)



    {% load xxx %}


4. 使用simple_tag



    <span style="color: #000000">{% my_simple_time 1 2 3%}</span>






5. 在 settings中配置当前 app, 不然 Django 无法找到自定义的 simple_tag



    INSTALLED_APPS =<span style="color: #000000"> (
        </span><span style="color: #800000">'</span><span style="color: #800000">django.contrib.admin</span><span style="color: #800000">'</span><span style="color: #000000">,
        </span><span style="color: #800000">'</span><span style="color: #800000">django.contrib.auth</span><span style="color: #800000">'</span><span style="color: #000000">,
        </span><span style="color: #800000">'</span><span style="color: #800000">django.contrib.contenttypes</span><span style="color: #800000">'</span><span style="color: #000000">,
        </span><span style="color: #800000">'</span><span style="color: #800000">django.contrib.sessions</span><span style="color: #800000">'</span><span style="color: #000000">,
        </span><span style="color: #800000">'</span><span style="color: #800000">django.contrib.messages</span><span style="color: #800000">'</span><span style="color: #000000">,
        </span><span style="color: #800000">'</span><span style="color: #800000">django.contrib.staticfiles</span><span style="color: #800000">'</span><span style="color: #000000">,
        </span><span style="color: #800000">'</span><span style="color: #800000">web</span><span style="color: #800000">'</span><span style="color: #000000">,
    )</span>





* * *






**Form 验证**




Form 测试时, 会遇到一个 CSRF (伪造夸张请求) 的错误, 把 settings.py 里 MIDDLEWARE_CLASSES 中的 'django.middleware.csrf.CsrfViewMiddleware' 注释掉就可以了.




Form 验证可以使用 HTML 自带的 input 来发送数据, 也可以使用 Python 的 form(推荐).




**使用 HTML 的 form**




在 template 目录下建立一个 login.html 文件:




    <span style="color: #008000"><!--</span><span style="color: #008000"> HTML </span><span style="color: #008000">--></span>
    <span style="color: #0000ff"><!</span><span style="color: #ff00ff">DOCTYPE html</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"><</span><span style="color: #800000">html</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"><</span><span style="color: #800000">head </span><span style="color: #ff0000">lang</span><span style="color: #0000ff">="en"</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">meta </span><span style="color: #ff0000">charset</span><span style="color: #0000ff">="UTF-8"</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">title</span><span style="color: #0000ff">></</span><span style="color: #800000">title</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"></</span><span style="color: #800000">head</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"><</span><span style="color: #800000">body</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">form </span><span style="color: #ff0000">action</span><span style="color: #0000ff">=""</span><span style="color: #ff0000"> method</span><span style="color: #0000ff">="post"</span><span style="color: #0000ff">></span> <span style="color: #008000"><!--</span><span style="color: #008000"> action 不写, 就是 Post 当前网址 </span><span style="color: #008000">--></span>
            <span style="color: #0000ff"><</span><span style="color: #800000">p</span><span style="color: #0000ff">></span>First name: <span style="color: #0000ff"><</span><span style="color: #800000">input </span><span style="color: #ff0000">type</span><span style="color: #0000ff">="text"</span><span style="color: #ff0000"> name</span><span style="color: #0000ff">="fname"</span> <span style="color: #0000ff">/></</span><span style="color: #800000">p</span><span style="color: #0000ff">></span>
            <span style="color: #0000ff"><</span><span style="color: #800000">p</span><span style="color: #0000ff">></span>Last name: <span style="color: #0000ff"><</span><span style="color: #800000">input </span><span style="color: #ff0000">type</span><span style="color: #0000ff">="text"</span><span style="color: #ff0000"> name</span><span style="color: #0000ff">="lname"</span> <span style="color: #0000ff">/></</span><span style="color: #800000">p</span><span style="color: #0000ff">></span>
            <span style="color: #0000ff"><</span><span style="color: #800000">input </span><span style="color: #ff0000">type</span><span style="color: #0000ff">="submit"</span><span style="color: #ff0000"> value</span><span style="color: #0000ff">="Submit"</span> <span style="color: #0000ff">/></span>
            <span style="color: #0000ff"><</span><span style="color: #800000">span</span><span style="color: #0000ff">></span>{{status}}<span style="color: #0000ff"></</span><span style="color: #800000">span</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"></</span><span style="color: #800000">form</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"></</span><span style="color: #800000">body</span><span style="color: #0000ff">></span>





修改 views.py:



    <span style="color: #008000">#</span><span style="color: #008000"> python</span>
    <span style="color: #0000ff">def</span><span style="color: #000000"> Login(request):
        </span><span style="color: #008000">#</span><span style="color: #008000">print request.method</span>
        <span style="color: #0000ff">if</span> request.method == <span style="color: #800000">'</span><span style="color: #800000">POST</span><span style="color: #800000">'</span><span style="color: #000000">:
            fname </span>= request.POST.get(<span style="color: #800000">'</span><span style="color: #800000">fname</span><span style="color: #800000">'</span>, None) <span style="color: #008000">#</span><span style="color: #008000"> 第二个参数是默认返回值</span>
            lname = request.POST.get(<span style="color: #800000">'</span><span style="color: #800000">lname</span><span style="color: #800000">'</span><span style="color: #000000">, None)
            result </span>= UserInfo.objects.filter(username=fname, password=<span style="color: #000000">lname).count()
            </span><span style="color: #0000ff">if</span> result == 1<span style="color: #000000">:
                </span><span style="color: #0000ff">return</span> HttpResponse(<span style="color: #800000">'</span><span style="color: #800000">登录成功</span><span style="color: #800000">'</span><span style="color: #000000">)
            </span><span style="color: #0000ff">else</span><span style="color: #000000">:
                </span><span style="color: #0000ff">return</span> render_to_response(<span style="color: #800000">'</span><span style="color: #800000">login.html</span><span style="color: #800000">'</span>, {<span style="color: #800000">'</span><span style="color: #800000">status</span><span style="color: #800000">'</span>:<span style="color: #800000">'</span><span style="color: #800000">用户名或密码错误</span><span style="color: #800000">'</span><span style="color: #000000">})

        </span><span style="color: #0000ff">return</span> render_to_response(<span style="color: #800000">'</span><span style="color: #800000">login.html</span><span style="color: #800000">'</span>)


之所以用 filter 而不用 get, 是因为 get 方法如果结果为空时会报异常.



最后在 urls.py 里做映射, 然后通过 **localhost:8000/web/login/** 就能进入 Login 函数, 然后返回 login.html 开始登录操作.



    urlpatterns =<span style="color: #000000"> [
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^login/</span><span style="color: #800000">'</span><span style="color: #000000">, Login),
    ]</span>






**使用 Python 的 form**




1. 在 web 下新建一个 forms.py 文件



    <span style="color: #008000">#</span><span style="color: #008000">-*- coding:utf-8 -*-</span>

    <span style="color: #0000ff">from</span> django <span style="color: #0000ff">import</span><span style="color: #000000"> forms

    </span><span style="color: #008000">#</span><span style="color: #008000">#######################################################################</span>
    <span style="color: #0000ff">class</span><span style="color: #000000"> RegisterForm(forms.Form):
        </span><span style="color: #800000">"""</span><span style="color: #800000">
        注册用 Form
        </span><span style="color: #800000">"""</span><span style="color: #000000">

        userName </span>=<span style="color: #000000"> forms.CharField()
        email </span>= forms.EmailField(required=True)






2. 在 template 目录中 regist.html



    <span style="color: #0000ff"><!</span><span style="color: #ff00ff">DOCTYPE html</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"><</span><span style="color: #800000">html</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"><</span><span style="color: #800000">head </span><span style="color: #ff0000">lang</span><span style="color: #0000ff">="en"</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">meta </span><span style="color: #ff0000">charset</span><span style="color: #0000ff">="UTF-8"</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">title</span><span style="color: #0000ff">></</span><span style="color: #800000">title</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"></</span><span style="color: #800000">head</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"><</span><span style="color: #800000">body</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">form </span><span style="color: #ff0000">action</span><span style="color: #0000ff">=""</span><span style="color: #ff0000"> method</span><span style="color: #0000ff">="post"</span><span style="color: #0000ff">></span> <span style="color: #008000"><!--</span><span style="color: #008000"> action 不写, 就是 Post 当前网址 </span><span style="color: #008000">--></span>
            <span style="color: #0000ff"><</span><span style="color: #800000">p</span><span style="color: #0000ff">></span>账号: {{form.userName}}<span style="color: #0000ff"></</span><span style="color: #800000">p</span><span style="color: #0000ff">></span>
            <span style="color: #0000ff"><</span><span style="color: #800000">p</span><span style="color: #0000ff">></span>邮箱: {{form.email}}<span style="color: #0000ff"></</span><span style="color: #800000">p</span><span style="color: #0000ff">></span>
            <span style="color: #0000ff"><</span><span style="color: #800000">input </span><span style="color: #ff0000">type</span><span style="color: #0000ff">="submit"</span><span style="color: #ff0000"> value</span><span style="color: #0000ff">="Submit"</span> <span style="color: #0000ff">/></span>
            <span style="color: #0000ff"><</span><span style="color: #800000">span</span><span style="color: #0000ff">></span>{{status}}<span style="color: #0000ff"></</span><span style="color: #800000">span</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"></</span><span style="color: #800000">form</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"></</span><span style="color: #800000">body</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"></</span><span style="color: #800000">html</span><span style="color: #0000ff">></span>

可以看到, 省略了 input 标签, 而改用 {{form.xxx}}.




3. 在 views.py 中添加函数



    <span style="color: #0000ff">from</span> forms <span style="color: #0000ff">import</span><span style="color: #000000"> RegisterForm

    </span><span style="color: #0000ff">def</span><span style="color: #000000"> Regist(request):
        </span><span style="color: #008000">#</span><span style="color: #008000">print request.method</span>
        <span style="color: #0000ff">if</span> request.method == <span style="color: #800000">'</span><span style="color: #800000">POST</span><span style="color: #800000">'</span><span style="color: #000000">:
            regist </span>=<span style="color: #000000"> RegisterForm(request.POST)
            </span><span style="color: #0000ff">if</span> regist.is_valid(): <span style="color: #008000">#</span><span style="color: #008000"> 如果格式满足要求, 例如 邮箱必须是有 @ 的</span>
                data = regist.cleaned_data <span style="color: #008000">#</span><span style="color: #008000"> 获取数据</span>
                result = <span style="color: #800000">'</span><span style="color: #800000">注册成功, 账号:%s  邮箱:%s</span><span style="color: #800000">'</span> % (data[<span style="color: #800000">'</span><span style="color: #800000">userName</span><span style="color: #800000">'</span>], data[<span style="color: #800000">'</span><span style="color: #800000">email</span><span style="color: #800000">'</span><span style="color: #000000">])
                </span><span style="color: #0000ff">return</span><span style="color: #000000"> HttpResponse(result)
            </span><span style="color: #0000ff">else</span><span style="color: #000000">:
                </span><span style="color: #008000">#</span><span style="color: #008000"> 验证失败</span>
                <span style="color: #0000ff">return</span><span style="color: #000000"> HttpResponse(regist.errors.as_json())
        </span><span style="color: #0000ff">else</span><span style="color: #000000">:
            regist </span>=<span style="color: #000000"> RegisterForm()
            </span><span style="color: #0000ff">return</span> render_to_response(<span style="color: #800000">'</span><span style="color: #800000">regist.html</span><span style="color: #800000">'</span>, {<span style="color: #800000">'</span><span style="color: #800000">form</span><span style="color: #800000">'</span>:regist})


4. 最后在 urls.py 中映射下即可.

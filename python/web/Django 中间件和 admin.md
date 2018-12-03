在django中, 中间件其实就是一个类, 在请求到来和结束后, django 会根据自己的规则在合适的时机执行中间件中相应的方法.

在 django 项目的 settings.py 模块中, 有一个 MIDDLEWARE_CLASSES 变量, 其中每一个元素就是一个中间件, 如下图.
![](http://i3.tietuku.com/01e947913cfd69a0.png)

除了上面的中间件外, 还有两个常用的中间件:
**django.middleware.gzip.GZipMiddleware** # 用来压缩浏览器返回的内容, 减少服务器带宽
**django.middleware.http.SetRemoteAddrFromForwardedFor** # 处理反向代理

* * *



中间件中可以定义四个方法, 分别是:
**process_request(self, request) -- 请求刚发起时
process_view(self, request, callback, callback_args, callback_kwargs) -- 进入 views.py 模块之前
process_exception(self, request, exception) -- 异常时
process_response(self, request, response) -- views.py 返回时**
以上方法的返回值可以是 None 和 HttpResonse 对象，如果是 None, 则继续向下执行, 如果是 HttpResonse 对象, 则直接将该对象返回给用户.

**自定义中间件


    <span style="color: #008000">#</span><span style="color: #008000"> middleware.py</span>
    <span style="color: #0000ff">class</span><span style="color: #000000"> RequestExeute(object):
        </span><span style="color: #0000ff">def</span><span style="color: #000000"> process_request(self,request):
            </span><span style="color: #0000ff">pass</span>

        <span style="color: #0000ff">def</span><span style="color: #000000"> process_view(self, request, callback, callback_args, callback_kwargs):
            </span><span style="color: #0000ff">pass</span>

        <span style="color: #0000ff">def</span><span style="color: #000000"> process_exception(self, request, exception):
            </span><span style="color: #0000ff">pass</span>

        <span style="color: #0000ff">def</span><span style="color: #000000"> process_response(self, request, response):
            </span><span style="color: #0000ff">return</span> response

**







    <span style="color: #008000">#</span><span style="color: #008000"> 注册中间件</span>
    MIDDLEWARE_CLASSES =<span style="color: #000000"> (
        </span><span style="color: #800000">'</span><span style="color: #800000">middleware.RequestExeute</span><span style="color: #800000">'</span><span style="color: #000000">,
    )</span>

* * *






**admin**




amdin 是 django 提供的一个后台管理页面, 改管理页面提供完善的 html 和 css, 使得你在通过 Model 创建完数据库表之后, 就可以对数据进行增删改查, 而使用 django admin 则需要以下步骤:




**1. 创建后台管理员**
python manage.py createsuperuser




**2. 配置后台管理url**
url(r'^admin/', include(admin.site.urls))




**3. 注册和配置 django admin 后台管理页面**




a、在admin中执行如下配置



    <span style="color: #0000ff">from</span> django.contrib <span style="color: #0000ff">import</span><span style="color: #000000"> admin

    </span><span style="color: #0000ff">from</span> web <span style="color: #0000ff">import</span><span style="color: #000000"> models

    admin.site.register(models.UserType)</span>


b、设置数据表名称



    <span style="color: #0000ff">class</span><span style="color: #000000"> UserType(models.Model):
        name </span>= models.CharField(max_length=50<span style="color: #000000">)

        </span><span style="color: #0000ff">class</span><span style="color: #000000"> Meta:
            verbose_name </span>= <span style="color: #800000">'</span><span style="color: #800000">用户类型</span><span style="color: #800000">'</span><span style="color: #000000">
            verbose_name_plural </span>= <span style="color: #800000">'</span><span style="color: #800000">用户类型</span><span style="color: #800000">'</span>


c、打开表之后, 设定默认显示, 需要在 model 中作如下配置



    <span style="color: #0000ff">class</span><span style="color: #000000"> UserType(models.Model):
        name </span>= models.CharField(max_length=50<span style="color: #000000">)

        </span><span style="color: #0000ff">def</span> <span style="color: #800080">__unicode__</span><span style="color: #000000">(self):
            </span><span style="color: #0000ff">return</span> self.name





    <span style="color: #0000ff">from</span> django.contrib <span style="color: #0000ff">import</span><span style="color: #000000"> admin

    </span><span style="color: #0000ff">from</span> web <span style="color: #0000ff">import</span><span style="color: #000000">  models

    </span><span style="color: #0000ff">class</span><span style="color: #000000"> UserInfoAdmin(admin.ModelAdmin):
        list_display </span>= (<span style="color: #800000">'</span><span style="color: #800000">username</span><span style="color: #800000">'</span>, <span style="color: #800000">'</span><span style="color: #800000">password</span><span style="color: #800000">'</span>, <span style="color: #800000">'</span><span style="color: #800000">email</span><span style="color: #800000">'</span><span style="color: #000000">)

    admin.site.register(models.UserType)
    admin.site.register(models.UserInfo,UserInfoAdmin)</span>


d、为数据表添加搜索功能



    <span style="color: #0000ff">from</span> django.contrib <span style="color: #0000ff">import</span><span style="color: #000000"> admin

    </span><span style="color: #0000ff">from</span> web <span style="color: #0000ff">import</span><span style="color: #000000">  models

    </span><span style="color: #0000ff">class</span><span style="color: #000000"> UserInfoAdmin(admin.ModelAdmin):
        list_display </span>= (<span style="color: #800000">'</span><span style="color: #800000">username</span><span style="color: #800000">'</span>, <span style="color: #800000">'</span><span style="color: #800000">password</span><span style="color: #800000">'</span>, <span style="color: #800000">'</span><span style="color: #800000">email</span><span style="color: #800000">'</span><span style="color: #000000">)
        search_fields </span>= (<span style="color: #800000">'</span><span style="color: #800000">username</span><span style="color: #800000">'</span>, <span style="color: #800000">'</span><span style="color: #800000">email</span><span style="color: #800000">'</span><span style="color: #000000">)

    admin.site.register(models.UserType)
    admin.site.register(models.UserInfo,UserInfoAdmin)</span>






e、添加快速过滤



    <span style="color: #0000ff">from</span> django.contrib <span style="color: #0000ff">import</span><span style="color: #000000"> admin

    </span><span style="color: #0000ff">from</span> web <span style="color: #0000ff">import</span><span style="color: #000000">  models

    </span><span style="color: #0000ff">class</span><span style="color: #000000"> UserInfoAdmin(admin.ModelAdmin):
        list_display </span>= (<span style="color: #800000">'</span><span style="color: #800000">username</span><span style="color: #800000">'</span>, <span style="color: #800000">'</span><span style="color: #800000">password</span><span style="color: #800000">'</span>, <span style="color: #800000">'</span><span style="color: #800000">email</span><span style="color: #800000">'</span><span style="color: #000000">)
        search_fields </span>= (<span style="color: #800000">'</span><span style="color: #800000">username</span><span style="color: #800000">'</span>, <span style="color: #800000">'</span><span style="color: #800000">email</span><span style="color: #800000">'</span><span style="color: #000000">)
        list_filter </span>= (<span style="color: #800000">'</span><span style="color: #800000">username</span><span style="color: #800000">'</span>, <span style="color: #800000">'</span><span style="color: #800000">email</span><span style="color: #800000">'</span><span style="color: #000000">)

    admin.site.register(models.UserType)
    admin.site.register(models.UserInfo,UserInfoAdmin)</span>


更多[: http://docs.30c.org/djangobook2/chapter06/](http://docs.30c.org/djangobook2/chapter06/)



**设置 admin 用户: python manage.py createsuperuser**
0?

这个应该也是 Django 比较特色的东西, 就是防止没有标识的 post 请求, 这个标识在 Django 中被称为 token.

它通过 **MIDDLEWARE_CLASSES** 中的 **django.middleware.csrf.CsrfViewMiddleware** 开关控制.

这个标识其实也是 cookie 值, 通过简单的两步代码就可以给请求打上标识.

Django 端


    render_to_response(<span style="color: #800000">'</span><span style="color: #800000">login.html</span><span style="color: #800000">'</span>, {}, context_instance=RequestContext(request))


HTML 端



    <span style="color: #0000ff"><</span><span style="color: #800000">form</span><span style="color: #0000ff">></span><span style="color: #000000">
    {%csrf_token%}
    ...
    </span><span style="color: #0000ff"></</span><span style="color: #800000">form</span><span style="color: #0000ff">></span>






如果不是在 form 中, 而是在 ajax 中的那话, 那么需要额外对 ajax 进行设置:



    <span style="color: #0000ff"><!</span><span style="color: #ff00ff">DOCTYPE html</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"><</span><span style="color: #800000">html</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"><</span><span style="color: #800000">head </span><span style="color: #ff0000">lang</span><span style="color: #0000ff">="en"</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">meta </span><span style="color: #ff0000">charset</span><span style="color: #0000ff">="UTF-8"</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">title</span><span style="color: #0000ff">></</span><span style="color: #800000">title</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"></</span><span style="color: #800000">head</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"><</span><span style="color: #800000">body</span><span style="color: #0000ff">></span><span style="color: #000000">
        {% csrf_token %}

        </span><span style="color: #0000ff"><</span><span style="color: #800000">input </span><span style="color: #ff0000">type</span><span style="color: #0000ff">="button"</span><span style="color: #ff0000"> onclick</span><span style="color: #0000ff">="Do();"</span><span style="color: #ff0000">  value</span><span style="color: #0000ff">="Do it"</span><span style="color: #0000ff">/></span>

        <span style="color: #0000ff"><</span><span style="color: #800000">script </span><span style="color: #ff0000">src</span><span style="color: #0000ff">="/static/plugin/jquery/jquery-1.8.0.js"</span><span style="color: #0000ff">></</span><span style="color: #800000">script</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">script </span><span style="color: #ff0000">src</span><span style="color: #0000ff">="/static/plugin/jquery/jquery.cookie.js"</span><span style="color: #0000ff">></</span><span style="color: #800000">script</span><span style="color: #0000ff">></span>
        <span style="color: #0000ff"><</span><span style="color: #800000">script </span><span style="color: #ff0000">type</span><span style="color: #0000ff">="text/javascript"</span><span style="color: #0000ff">></span>
            <span style="color: #0000ff; background-color: #f5f5f5">var</span><span style="color: #000000; background-color: #f5f5f5"> csrftoken </span><span style="color: #000000; background-color: #f5f5f5">=</span><span style="color: #000000; background-color: #f5f5f5"> $.cookie(</span><span style="color: #000000; background-color: #f5f5f5">'</span><span style="color: #000000; background-color: #f5f5f5">csrftoken</span><span style="color: #000000; background-color: #f5f5f5">'</span><span style="color: #000000; background-color: #f5f5f5">);

            </span><span style="color: #0000ff; background-color: #f5f5f5">function</span><span style="color: #000000; background-color: #f5f5f5"> csrfSafeMethod(method) {
                </span><span style="color: #008000; background-color: #f5f5f5">//</span><span style="color: #008000; background-color: #f5f5f5"> these HTTP methods do not require CSRF protection</span>
                <span style="color: #0000ff; background-color: #f5f5f5">return</span><span style="color: #000000; background-color: #f5f5f5"> (</span><span style="color: #000000; background-color: #f5f5f5">/</span><span style="color: #000000; background-color: #f5f5f5">^(GET|HEAD|OPTIONS|TRACE)$</span><span style="color: #000000; background-color: #f5f5f5">/</span><span style="color: #000000; background-color: #f5f5f5">.test(method));
            }
            $.ajaxSetup({
                beforeSend: </span><span style="color: #0000ff; background-color: #f5f5f5">function</span><span style="color: #000000; background-color: #f5f5f5">(xhr, settings) {
                    </span><span style="color: #0000ff; background-color: #f5f5f5">if</span><span style="color: #000000; background-color: #f5f5f5"> (</span><span style="color: #000000; background-color: #f5f5f5">!</span><span style="color: #000000; background-color: #f5f5f5">csrfSafeMethod(settings.type) </span><span style="color: #000000; background-color: #f5f5f5">&&</span> <span style="color: #000000; background-color: #f5f5f5">!</span><span style="color: #0000ff; background-color: #f5f5f5">this</span><span style="color: #000000; background-color: #f5f5f5">.crossDomain) {
                        xhr.setRequestHeader(</span><span style="color: #000000; background-color: #f5f5f5">"</span><span style="color: #000000; background-color: #f5f5f5">X-CSRFToken</span><span style="color: #000000; background-color: #f5f5f5">"</span><span style="color: #000000; background-color: #f5f5f5">, csrftoken);
                    }
                }
            });
            </span><span style="color: #0000ff; background-color: #f5f5f5">function</span><span style="color: #000000; background-color: #f5f5f5"> Do(){
                $.ajax({
                    url:</span><span style="color: #000000; background-color: #f5f5f5">"</span><span style="color: #000000; background-color: #f5f5f5">/app01/test/</span><span style="color: #000000; background-color: #f5f5f5">"</span><span style="color: #000000; background-color: #f5f5f5">,
                    data:{id:</span><span style="color: #000000; background-color: #f5f5f5">1</span><span style="color: #000000; background-color: #f5f5f5">},
                    type:</span><span style="color: #000000; background-color: #f5f5f5">'</span><span style="color: #000000; background-color: #f5f5f5">POST</span><span style="color: #000000; background-color: #f5f5f5">'</span><span style="color: #000000; background-color: #f5f5f5">,
                    success:</span><span style="color: #0000ff; background-color: #f5f5f5">function</span><span style="color: #000000; background-color: #f5f5f5">(data){
                        console.log(data);
                    }
                });

            }
        </span><span style="color: #0000ff"></</span><span style="color: #800000">script</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"></</span><span style="color: #800000">body</span><span style="color: #0000ff">></span>
    <span style="color: #0000ff"></</span><span style="color: #800000">html</span><span style="color: #0000ff">></span>





通过两个装饰器, 可以对 Django 中的函数强制开启跨站请求伪造功能:
@csrf_protect, 为当前函数强制设置防跨站请求伪造功能, 即便 settings.py 中没有设置全局中间件;
@csrf_exempt, 取消当前函数防跨站请求伪造功能, 即便 settings.oy 中设置了全局中间件;
l(

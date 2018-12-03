直接上实战来看.

HTML 部分:


    <!DOCTYPE html>
    <html>
    <head lang=<span style="color: #800000">"</span><span style="color: #800000">en</span><span style="color: #800000">"</span>>
        <meta charset=<span style="color: #800000">"</span><span style="color: #800000">UTF-8</span><span style="color: #800000">"</span>>
        <title></title>
    </head>
    <body>
            <input type=<span style="color: #800000">"</span><span style="color: #800000">text</span><span style="color: #800000">"</span> id=<span style="color: #800000">"</span><span style="color: #800000">acc</span><span style="color: #800000">"</span>/>
            <input type=<span style="color: #800000">"</span><span style="color: #800000">button</span><span style="color: #800000">"</span> value=<span style="color: #800000">"</span><span style="color: #800000">ajax</span><span style="color: #800000">"</span> onclick=<span style="color: #800000">"</span><span style="color: #800000">DoAjax();</span><span style="color: #800000">"</span>/>
    </body>
    <script src=<span style="color: #800000">"</span><span style="color: #800000">{{ STATIC_URL }}jquery-1.11.3.js</span><span style="color: #800000">"</span>></script>
    <script type=<span style="color: #800000">"</span><span style="color: #800000">text/javascript</span><span style="color: #800000">"</span>><span style="color: #000000">
        function DoAjax() {
            var acc </span>= $(<span style="color: #800000">'</span><span style="color: #800000">#acc</span><span style="color: #800000">'</span><span style="color: #000000">).val();
            $.ajax({
                url:</span><span style="color: #800000">'</span><span style="color: #800000">web/ajax/</span><span style="color: #800000">'</span><span style="color: #000000">,
                type:</span><span style="color: #800000">'</span><span style="color: #800000">POST</span><span style="color: #800000">'</span><span style="color: #000000">,
                data:{dat:acc},
                success:function(arg) {
                    console.log(</span><span style="color: #800000">'</span><span style="color: #800000">success</span><span style="color: #800000">'</span><span style="color: #000000">);
                    console.log(arg); </span>//<span style="color: #000000"> 这个 arg 是返回内容
                },
                error:function() {
                    console.log(</span><span style="color: #800000">'</span><span style="color: #800000">error</span><span style="color: #800000">'</span><span style="color: #000000">);
                }
            })
        }
    </span></script>
    </html>






再看 Django 部分:



    urlpatterns =<span style="color: #000000"> [
        url(r</span><span style="color: #800000">'</span><span style="color: #800000">^ajax/</span><span style="color: #800000">'</span><span style="color: #000000">, DoAjax),
    ]
    </span><span style="color: #008000">#</span><span style="color: #008000">#################################</span>
    <span style="color: #0000ff">def</span><span style="color: #000000"> DoAjax(request):
        </span><span style="color: #008000">#</span><span style="color: #008000">print request.method</span>
        <span style="color: #0000ff">if</span> request.method == <span style="color: #800000">'</span><span style="color: #800000">POST</span><span style="color: #800000">'</span><span style="color: #000000">:
            </span><span style="color: #0000ff">return</span> HttpResponse(<span style="color: #800000">'</span><span style="color: #800000">OK</span><span style="color: #800000">'</span><span style="color: #000000">)
        </span><span style="color: #0000ff">return</span> render_to_response(<span style="color: #800000">'</span><span style="color: #800000">ajax.html</span><span style="color: #800000">'</span>, {}, context_instance=RequestContext(request))


当点击按钮触发 DoAjax() 时, 页面不会刷新, Django 会把返回的内容发送给 $.ajax 中 success:function(arg) 的参数.
**





* * *







发送 JSON 数据




    <span style="color: #0000ff">import</span><span style="color: #000000"> json

    </span><span style="color: #0000ff">def</span><span style="color: #000000"> DoAjax(request):
        </span><span style="color: #008000">#</span><span style="color: #008000">print request.method</span>
        <span style="color: #0000ff">if</span> request.method == <span style="color: #800000">'</span><span style="color: #800000">POST</span><span style="color: #800000">'</span><span style="color: #000000">:
            data </span>= {<span style="color: #800000">'</span><span style="color: #800000">status</span><span style="color: #800000">'</span><span style="color: #000000">:0,
                    </span><span style="color: #800000">'</span><span style="color: #800000">msg</span><span style="color: #800000">'</span>:<span style="color: #800000">'</span><span style="color: #800000">请求成功</span><span style="color: #800000">'</span><span style="color: #000000">,
                    </span><span style="color: #800000">'</span><span style="color: #800000">data</span><span style="color: #800000">'</span>:[11,22<span style="color: #000000">]}
            </span><span style="color: #0000ff">return</span><span style="color: #000000"> HttpResponse(json.dumps(data))
        </span><span style="color: #0000ff">return</span> render_to_response(<span style="color: #800000">'</span><span style="color: #800000">ajax.html</span><span style="color: #800000">'</span>)





    <script type="text/javascript">
        <span style="color: #0000ff">function</span><span style="color: #000000"> DoAjax() {
            </span><span style="color: #0000ff">var</span> acc = $('#acc'<span style="color: #000000">).val();
            $.ajax({
                url:</span>'web/ajax/'<span style="color: #000000">,
                type:</span>'POST'<span style="color: #000000">,
                data:{dat:acc},
                success:</span><span style="color: #0000ff">function</span><span style="color: #000000">(arg) {
                    console.log(</span>'success'<span style="color: #000000">);
                    </span><span style="color: #008000">//</span><span style="color: #008000">console.log(arg); // 这个 arg 是返回内容</span>
                    <span style="color: #0000ff">var</span> obj = $.parseJSON(arg); <span style="color: #008000">//</span><span style="color: #008000">解析 json</span>
    <span style="color: #000000">                console.log(obj.status);
                    console.log(obj.msg);
                    console.log(obj.data);
                },
                error:</span><span style="color: #0000ff">function</span><span style="color: #000000">() {
                    console.log(</span>'error'<span style="color: #000000">);
                }
            })
        }
    </span></script>

**
:ry

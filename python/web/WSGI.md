>读 **廖雪峰** 老师文章笔记

web 的本质是什么?

- browser 发起 http 请求
- server 收到请求, 生成 html
- server 把 html 作为 http 的响应 body 返回给 browser
- browser 收到 http 响应
- browser 从 http body 中取出 html 并显示

上述步骤中, 接受HTTP请求、解析HTTP请求、发送HTTP响应 都是苦力活, 如果自己来写这些底层代码, 起码得花个把月去读 HTTP 规范, 所以我们其实并不希望接触到这些底层操作.

幸好, 这些底层操作已经由专门的服务器软件实现好了, 它们提供了统一的接口, 让我们专心编写 Web 业务, 这个接口就是 **CGI: Common Gateway Interface**, 而 **WSGI: Web Server Gateway Interface** 可以说是特别为 python 设计的 CGI.

**不过要注意的是, WSGI/CGI 不是服务器, 不是 API, 不是 Python 模块, 更不是什么框架, 而是一种服务器和客户端交互的接口规范! 是规范!!**

对于 wsgi 我们不需要了解太多, 不过一个简单的例子还是有必要的.

我们新建一个 **hello.py**
```python
def application(environ, start_response):
    start_response('200 OK', [('Content-Type', 'text/html')])
    return '<h1>Hello, web!</h1>'
```
这个简单的 application() 函数就是一个符合 wsgi 标准的处理函数.

- environ: 一个包含所有 HTTP 请求信息的 dict 对象;
- start_response: 一个发送 HTTP 响应的函数.

在函数内部, 我们通过调用 start_response() 返回 http header, 通过 return 返回 http body.

刚才说了, http 的底层操作已经由专门的服务器软件实现好了, 那么这里我们的服务器软件是什么呢?

Python 内置了一个 WSGI 服务器, 这个模块叫 wsgiref, 它是用纯 Python 编写的 WSGI 服务器的参考实现, 所谓“参考实现”是指该实现完全符合 WSGI 标准, 但是不考虑任何运行效率, 仅供开发和测试使用; 现在新建一个 **server.py**
```python
from wsgiref.simple_server import make_server
# 导入我们自己编写的application函数:
from hello import application

# 创建一个服务器，IP 地址为空，端口是8000，处理函数是 application:
httpd = make_server('', 8000, application)
print "Serving HTTP on port 8000..."
# 开始监听HTTP请求:
httpd.serve_forever()
```
然后, 运行 **python server.py** 启动 wsgi 服务器, 打开浏览器, 访问 localhost:8000, 看看是不是出现了什么效果~~

现在已经了解了 WSGI 框架, 我们发现, 其实一个 Web App, 就是写一个个的 wsgi 处理函数, 针对每个 HTTP 请求进行响应;

但是代码如果按 hello.py 里那样写, 那就要在每个函数内部判断是哪个 url 过来的, 是 get 的还是 post, 一个两个函数没有问题, 如果100 个甚至 1000 个不同的 URL 请求, 那代码会显得非常臃肿, 难以维护, 所以我们要在 wsgi 之上再抽象出 Web 框架, 让函数只处理业务逻辑, 而 url 的分发让 Web 框架来处理.

现在网上是有一些流行的Web框架:

- Django: 一站式开发框架, 但不利于定制化;
- web.py: 使用类而不是更简单的函数来处理URL, 并且 URL 映射是单独配置的;
- Flask: 使用 @decorator 的 URL 路由不错, 但框架对应用程序的代码入侵太强;
- bottle: 缺少根据 URL 模式进行拦截的功能, 不利于做权限检查.

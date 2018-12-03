Python fabric 号称 Python 三神器之一, 可以在本地、远程依次执行一系列 shell 命令、程序等, 比如从代码库更新代码、执行数据迁移脚本、重启服务进程等等, 因此常常被用来自动化远程部署项目, 非常的方便.

当然你要说 __Docker__ 更好, 我也同意, 然而有一些 32 位的低配系统, 并不适合用 __Docker__, 更不用说虚拟机了, 这时一个轻量级的 fabric 就能帮你解决部署问题.

# 1. 安装
虽然 fabric 是支持 pip 来安装的, 但实际操作时, 安装 fabric 的依赖库 pycrypto 会报错, 大概的意思就是编译环境的问题.

这是因为 fabric 涉及了一些系统底层的东西, 凡是安装和操作系统底层密切相关的扩展, 大多都会调用 C 编译器去编译一些系统 DLL, 如果你的系统没有这个编译器, 就会报错...

有几种解决方法 (__推荐第三种中的第二种__):

- 第一种: 一般这类的扩展都提供 windows 的安装版本([msi 或者 exe 执行文件](http://www.voidspace.org.uk/python/modules.shtml#pycrypto))
- 第二种: 下载[源码](http://share.weiyun.com/8d44e45a0b9186c3dc03730a65eb643c), 手动使用 `python setup.py install --skip-build` 编译
- 第三种: 搭建编译环境, 这里也有三种方式:
    + 第一种: 借助 mingw32, 具体操作看下面 `借助 MinGW`
    + 第二种: 借助 [Microsoft Visual C++ Compiler Package for Python 2.7](http://www.microsoft.com/en-us/download/details.aspx?id=44266), 这里有可能会出现问题, 详情见下面的 `解决 Microsoft Visual C++ Compiler Package for Python 2.7 无效`
    + 第三种: 借助 VS2010, 这个比较麻烦, 具体操作看下面 `借助 VS2010`
    + 第二种 \> 第三种 \> 第一种

参考文档:<br>
[http://my.oschina.net/zhangdapeng89/blog/54407](http://my.oschina.net/zhangdapeng89/blog/54407)<br>
[http://www.myexception.cn/windows/414562.html](http://www.myexception.cn/windows/414562.html)<br>
[http://blog.csdn.net/donger_soft/article/details/44838109](http://blog.csdn.net/donger_soft/article/details/44838109)<br>
[http://www.2cto.com/os/201506/409745.html](http://www.2cto.com/os/201506/409745.html)

## 借助 MinGW
首先安装 MinGW, 在 MinGW 的安装目录下找到 bin 的文件夹, 找到 mingw32-make.exe, 复制一份更名为 make.exe;

把 MinGW 的路径添加到环境变量 path 中, 比如我把 MinGW 安装到 D:\MinGW\ 中, 就把 D:\MinGW\bin 添加到 path 中;

打开命令行窗口, 在命令行窗口中进入到要安装代码的目录下, 输入如下命令就可以安装了:

```
python setup.py build --compiler=mingw32 install
```

## 借助 VS2010
去下载安装 VS2010 (08版貌似也行, 不过没必要用旧版, 指不定哪个库又无法编译)

配置系统环境变量 __VS90COMNTOOLS__ 指向 `%VS你的版本COMNTOOLS%`

- 如果你安装的是 2012 版 `%VS110COMNTOOLS%`
- 如果你安装的是 2013 版 `%VS120COMNTOOLS%`

或者更暴力, 在 python 安装路径 ~\Lib\distutils 目录下有个 __msvc9compiler.py__(这个就是为什么要配 "VS90COMNTOOLS" 的原因, 因为人家文件名都告诉你了是 Microsoft vc 9 compiler, 代码都写死了要 vc9 的 comntools).<br>
找到 243 行:

```
toolskey = "VS%0.f0COMNTOOLS" % version
直接改为
toolskey = "VS你的版本COMNTOOLS"

```

然后该重启的重启, clean 一下之前安装 Python 扩展失败的残留文件, 然后该 pip 的 pip 吧...

```
pip install fabric
```

## 解决 Microsoft Visual C++ Compiler Package for Python 2.7 无效
__错误描述:__<br>
明明从官网下载并安装了 Microsoft Visual C++ Compiler Package for Python 2.7, 且配置了环境变量 path, 但还是报编译错误.

__错误原因:__<br>
报这个错误的原因是 Python 的 distutils 模块中的 msvc9compiler.py 并不从环境变量指定的路径中寻找 'vcvarsall.bat', 而是通过注册表来寻找, 然而, 不知为什么编译器安装过程没有配置注册表.

__解决办法:__<br>
只要手工把注册表配置好, 就可以了.

- 开注册表编辑器
    + run regedit
- 配置
    + 如果你安装的 Python 是 32 位的, 则创建如下项: `HKEY_CURRENT_USER\Software\Microsoft\VisualStudio\9.0\Setup\VC`
    + 如果你安装的 Python 是 64 位的, 则创建如下项: `HKEY_CURRENT_USER\Software\Wow6432Node\Microsoft\VisualStudio\9.0\Setup\VC`
- 并在此项下新建字符串值
    + 名称:productdir
    + 数据:vcvarsall.bat所在路径
    + 注意:路径中不包含最后的反斜杠.

# 2. 第一个 fabric 脚本
我这里使用的是 virtualenv 环境, 安装 pycrypto 使用的是 __第一种__ 方法 (即直接下载 exe 安装的), 所以需要拷贝系统 python ~\Lib\site-packages 下的 __Crypto__ 文件夹到 virtualenv 中, 然后 cmd 进入虚拟环境(即执行 activate), 安装 fabric: `pip install fabric`

安装好 fabric 后, 新建一个 py 脚本: __fabfile.py__

```python
def hello():
    print("Hello world!")
```

命令行执行:

```
(venv) fab hello
Hello world!

Done.
```

__fabfile__ 是 fabric 默认的脚本名, 可以不用 __fabfile__, 但是在执行时就需要通过 __-f__ 指定文件名了, 例如把上面的 fabfile.py 改为 __test.py__

```
(venv) fab hello

Fatal error: Couldn't find any fabfiles!

Remember that -f can be used to specify fabfile path, and use -h for help.

Aborting.

(venv) fab -f test.py hello
Hello world!

Done.
```


> 找不到 fab 命令<br>这种情况只会出现在 python 真实环境, 因为有可能没有权限改环境变量,
fab.exe 在 python 安装目录的 Scripts 目录, 要把该路径加入到环境变量 Path 中.

> 执行 fab 提示找不到 fabfiles<br>
原因: 跟 python 的 PYTHONPATH 有关系, 如果 fabfile.py 没有放在 PYTHONPATH 中, 会提示找不到.<br>
解决:使用 -f 指定文件

# 3. 带参数的脚本
```python
def hello(name, value):
    print("%s = %s!" % (name, value))
```

执行:

```
(venv) fab hello:name=age, value=20
age = 20!

Done.
(venv) fab hello:age, 20
age = 20!

Done.
```

# 4. 执行本地操作
在 fabric 中:
caputure=False 时,本地 subprocess 的可通过全局输出控制,output.stdout 等,此时返回为空。
caputure=True 时,命令 stdout 作为类 string 对象返回,同 run/sudo 一样,返回值有 return_code,stderr,failed 和 succeeded 属性。

- local: 用来执行本地命令
    + 可选参数 capture 布尔型
        * True, 标准输出和错误作为一个对象返回, 通过 `print result.__dict__` 发现该对象有下面这些可用属性:
            - succeeded
            - failed
            - return\_code
            - command
            - stderr
            - stdout
            - real\_command
        * False, 标准输出和错误直接显示在终端, 和 True 时一样, 也有一个返回值对象, 但 stderr 和  stdout 始终为空
- lcd: 本地的上下文管理, 简单的理解就是本地执行 cd 命令, 需要配合 with 语句
    + **注意**: 由于 windows 的尿性, **cd** 命令不能进行根目录间 (C 盘、D 盘等) 的切换

```python
from fabric.api import local, lcd

def dirCurrent():
    with lcd('./Current'):
        local('dir')
```

结果:

```
(venv) fab dirCurrent
[localhost] local: cd ./Current
[localhost] local: dir
E:\venv\Current 的目录

2016/04/20  16:47    <DIR>          .
2016/04/20  16:47    <DIR>          ..
2016/04/14  20:17                12 .gitignore
2016/04/19  21:02             3,907 MysqlHelper.py
2016/04/19  21:02             3,594 MysqlHelper.pyc

Done.
```

我们可以通过 fabric 完成 Git 版本库的日常维护, 例如:

```python
from fabric.api import local, lcd

def dailyGit():
    with lcd('./iMessageCracker'):
        local('git add .')
        local('git commit -m "daily update"')
```

# 5. 执行远程操作
在 fabric 中:

- run: 用来执行远程命令
    + 在 local 命令中, 我们通过可选参数 capture 来控制返回值的处理方式, 但在 run 命令中, 没有 capture 参数, run 在终端直接显示返回信息, 并产生一个返回值对象, 即 run 包含了 capture 的真/假两种情况
    + 如果需要编程检测远程程序的标准错误流 (利用函数返回值的 stderr 属性), 可以设置 `combine_stderr=False`, 这样当你远程调用一个程序产生标准错误流时, run 返回值的 stderr 属性就会保存这些信息, 虽然这么做会使终端输出很混乱, 但是这是单独获取标准错误流的唯一方式
- sudo: 用来执行远程 sudo 命令
- cd: 远程的上下文管理, 简单的理解就是远程执行 cd 命令, 需要配合 with 语句
- env: 环境变量
    + env.hosts, 远程主机列表,
        * 格式: user name @ ip : port
        * user name 及 port 可忽略, port 默认为 22
    + env.user, 远程主机用户名, 除非 env.hosts 显示的指定了 user name, 否则都用 env.user 配置的用户名
    + env.password, 如果指定, 则远程主机列表里的主机都使用这个密码
    + env.passwords, 密码列表, key 是 host, value 是密码
        * 要使 env.passwords 生效, host 的端口号一定要显式写出来, 即使是使用的默认 22 端口
- get: 下载文件 (前提是有待处理文件的权限)
    + 不能操作文件夹, 可以打包后下载
- put: 上传文件 (前提是有待处理文件的权限)
    + 不能操作文件夹, 可以打包后上传
- prefix: 命令上下文管理, 需要配合 with 语句
    + 处在 prefix 上下文环境下的命令, 都会加上 prefix 里指定的前缀

```python
# prefix 举例

with prefix('workon myvenv'):
        run('ls')
```

相当于执行命令: `workon myvenv && ls`

```python
# 远程操作举例

from fabric.api import local, cd, run, env

env.hosts = ['user name@ip:port',]
env.passwords = {
    'user name@ip:port': 'xxx'
}

def remoteGit():
    with cd('/usr/project/'):
        run('git --version')
```

执行结果:

```
[user name@10.0.3.57] Executing task 'remoteGit'
[user name@10.0.3.57] run: git --version
[user name@10.0.3.57] out: git version 1.7.9.5
[user name@10.0.3.57] out:

Done.
Disconnecting from user name@10.0.3.57... done.
```

# 6. 基于角色的执行任务
在 `5. 执行远程操作` 的例子中, fabric 会将任务应用到所有 env.hosts 中指定的 hosts.

通过 `env.roledefs` 可以指定一个任务只运行到某个或某几个 hosts 上.

env.roledefs 的是一个字典, key 为 role 名 (自定义就好), value 是 host 列表 (如: ['10.1.6.186', '10.1.6.159'])

env.passwords 的 key 来自 env.roledefs 的 value.

```python
from fabric.api import local, cd, run, env

env.roledefs = {'web1': [host 列表], 'web2': [host 列表]}
env.passwords = {
    'user name@ip:port': 'xxx'
}

@roles('web1')
def remoteGit():
    with cd('/usr/project/'):
        run('git --version')
```

上面的 remoteGit 就只会被运行在指定的 __web1__ 上了.

# 7. 项目同步工具
`from fabric.contrib.project import rsync_project` 是个很好的工具, 不过只有 linux 下能用... 因为 windows 上没有 __rsync__.

```python
from fabric.contrib.project import rsync_project

rsync_project(local_dir = 'xxx', remote_dir = '/home/user name/xxx', exclude = '.git')
```

与之功能相似的还有 `fabric.contrib.project.upload_project`

# 8. 新风格任务
在 fabric 1.1 之后, fabric 引进两种不同的任务定义方法: 新风格和经典风格

它们在判断 fabfile 中什么对象才是 fabric 任务时方式不同:

- __经典风格__认为 fabfile 中的所有 public 可调用对象 (包括 functions, classes 等等) 为任务, 而且仅仅是 fabfile 中的对象才行, fabfile 中 import 的其他模块都不算任务(但通过 from 引入的算任务)
- __新风格__认为 Task (新风格引用的任务基类) 的实例或者它的子类为任务, 并且可以递归 import 模块中的任务, 而且支持不同的命名空间(\_\_init\_\_.py)

这两种定义方式是__相互排斥__的, 如果 fabric 发现任何新风格的任务对象, 它就会认为你已经使用这种新风格的任务声明, 而不会考虑任何没有 __Task__ 声明的对象, 如果没有新风格任务发现, 它会使用默认经典风格.

## 8.1 经典风格
在之前, 我们使用的都是经典任务风格, 是的, 那就是普通的 python 函数, 当没有新风格任务发现时, fabric 默认都是经典任务, 注意一下情况:

- 函数名以 \_\_ 下划线开头, python 认为是 private 的函数, fabric认为不是任务
- 函数中没有用到 fabric 自身 api, fabric认为不是任务
- fabric 经典风格不能递归 import 模块中的任务
    + 如果是通过 __from__ 引入, 则可算任务

```python
from urllib import urlopen
from fabric.api import run
def webservice_read():
    objects = urlopen('http://my/web/service/?foo=bar').read().split()
    print(objects)
```

fab 认为 urlopen 也是一个任务.

## 8.2 新风格
新风格任务的出现是为了对面向对象特性及命名空间提供支持。

通过引进 Task, 有两种方式来定义新任务。

- 定义常规的模块级别的函数并带有装饰器 __@task__, 这会直接将该函数转化为 Task 子类, 该函数名会被作为任务名, __@task__包含下面这些参数:
    + task_class: Task 的子类, 被用来装饰函数, 默认是 WrappedCallableTask
    + aliases: 一个可迭代的 string names, 被用来作为该函数的别名
    + alias: 跟 aliases 类似, 但只有一个 string 值而不是可迭代对象, 如果 aliases 同时存在, 前者优先
    + default: 布尔值, 指定被包装的函数为模块的默认任务(在只有 fabfile.py 一个文件时, 这个参数是没有意义的), 详情见下面的 `@task default`
- 继承 Task 类, 定义 run 方法, 然后要在模块级别进行实例化, name 属性会被作为任务名

> 注意, 如果有多个装饰器时, @task 必须做为第一个, 否则会出现一些奇怪的问题

```python
# 通过装饰器定义
from fabric.api import task, run

@task
def mytask(): # 任务名为 mytask
    run('a command')

# 通过继承 Task 类
class MyTask(Task):
    name = "deploy" # 任务名为 deploy
    def run(self):
        run('git clone foo')
        sudo('service apache2 restart')

instance = MyTask()
```

也可以结合这两种定义方式, 例如下面这个例子:

```python
from fabric.api import task
from fabric.tasks import Task

class CustomTask(Task):
    def __init__(self, func, myarg, *args, **kwargs):
        super(CustomTask, self).__init__(*args, **kwargs)
        self.func = func
        self.myarg = myarg

    def run(self, *args, **kwargs):
        return self.func(*args, **kwargs)

@task(task_class = CustomTask, myarg = 'value', alias = 'at')
def actual_task():
    pass
```

这相当于生成一个新任务:

```python
task_obj = CustomTask(actual_task, myarg = 'value')
```

当然, myarg 参数可以在运行任务时指定:

```
fab actual_task:myarg=value
```

这种组合的方式, 在你想用使用 class metaprogramming or similar techniques 时会有用.

### @task default
新建一个文件 deploy.py:

```python
from fabric.api import task

@task
def migrate():
    pass

@task
def full_deploy():
    migrate()
```

然后在 fabfile.py 中 import deploy, 再通过命令行 `fab -list` 查看当前可用任务:

```
Available commands:

    deploy.full_deploy
    deploy.migrate
```

现在我们把 full\_deploy 设为默认任务, 看看有什么效果:

```python
from fabric.api import task

@task
def migrate():
    pass

@task(default = True)
def full_deploy():
    migrate()
```

再次通过命令行 `fab -list` 查看当前可用任务:

```
Available commands:

    deploy
    deploy.full_deploy
    deploy.migrate
```

可以看到多了一个 deploy 任务, 它就是 deploy.py 的默认任务

# 9. 使用 virtualenv
想通过 `run('source ./venv/bin/activate')` 去进入 virtualenv 是不行的.

## 9.1. 调用远程的 virtualenv
官方提供的解决方案是使用 __prefix__, prefix 与 cd 类似, 是远程命令:

```python
with prefix('source ~/venv/bin/activate'):
    run('./manage.py syncdb')
```

或者, 可以使用第三方扩展 __fabric-virtualenv__.

直接使用 `pip install fabric-virtualenv` 即可安装, 使用起来也很简单:

```python
from fabvenv import virtualenv

with virtualenv('/home/venv'):
    run('pip freeze')
```

好了, 根据结果看看是不是输出了对应 virtualenv 下的扩展包.

__注意:__ fabric-virtualenv 内部使用的也是 prefix , 所以也只支持远程操作, 不是本地的.

## 9.2. 调用本地的 virtualenv
因为 prefix 是远程命令, 所以如果想添加本地支持的话, 就得自己来模拟了...

```python
import posixpath
from contextlib import contextmanager
import os

@contextmanager
def lvirtualenv(path):
    activate = posixpath.join(path, 'bin/activate')
    if not os.path.exists(activate):
        raise OSError("Cannot activate virtualenv %s" % path)
    activate = '. %s' % activate

    def local_call(command):
        return local('%s && %s' % (activate, command))

    yield local_call

def test():
    with lvirtualenv('/home/apuser/PyMC/pymc_venv') as local:
        local('pwd')
```

这样我们最终的输出结果就是:

```shell
[localhost] local: . /home/apuser/PyMC/pymc_venv/bin/activate && pwd
/home/apuser/PyMC/iMessageCracker/Deploy
```

# 10. 通过 python 来执行 task
在之前, 我们通过 __fab__ 工具来执行 task.

在 Fabric >=1.3 中, 可以使用 execute 来在 python 中运行一个 task, 就像下面这样:

```python
from fabric.api import local, execute

def test_for_execute():
    local('pip freeze')

if __name__ == '__main__':
    execute(test_for_execute)
```

不仅如此, execute 还能接受 task 的返回值, 只要在 task 中 return 就好了, execute 本身返回一个字典, task 的返回值将做为这个字典的项.

基于 fabric 编程时, 如果想知道命令的执行结果:

- local 命令需要设置 capture 参数
- run 命令直接使用返回值就可以了

# 11. 中文问题
使用 fabric 获取的标准输出流中的中文是被 gbk 编码的, 如果你最终存放结果的"地方"不支持 gbk 显示, 就要用 `xxx.decode('gbk')` 进行解码...

> 你可以通过 python 第三方工具包 chardet 来判断字符串编码方式: `chardet.detect(xxx)`

# 参考文档
[http://blog.csdn.net/raptor/article/details/50397658](http://blog.csdn.net/raptor/article/details/50397658)<br>
[http://paperplane.ruhoh.com/documentation/fabric/](http://paperplane.ruhoh.com/documentation/fabric/)<br>
[http://paperplane.ruhoh.com/fabric/fabric-api/](http://paperplane.ruhoh.com/fabric/fabric-api/)<br>
[http://docs.fabfile.org/en/latest/tutorial.html](http://docs.fabfile.org/en/latest/tutorial.html)<br>
[http://ruiaylin.github.io/2014/11/24/fabric/](http://ruiaylin.github.io/2014/11/24/fabric/)<br>
[http://wklken.me/posts/2013/03/25/python-tool-fabric.html#_4](http://wklken.me/posts/2013/03/25/python-tool-fabric.html#_4)

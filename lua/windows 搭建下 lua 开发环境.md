Windows 搭建 Lua 环境包含3部分内容：**编译lua的库文件(*.lib)、编译lua解释器(lua.exe)、编译lua编译器(luac.exe)**.
我们采用 VS2013 + lua-5.2.4 来进行编译.

VS2013 就不说了, 先去 Lua 官网下载 Lua 源码: [http://www.lua.org](http://www.lua.org)<br>
我这里下载的是 lua-5.2.4 版本.

![](http://i62.tinypic.com/1z1bmoo.jpg)

下载后解压, 我们得到以下文件, 其中 **src** 文件夹存放的就是 Lua 源码.

![](http://i57.tinypic.com/fxar8z.jpg)

OK, 现在我们来修改源码, **使 Lua 支持中文的变量名及函数名**.<br>
打开 src 文件夹下的 **llex.c** 文件, 定位到 **static int llex (LexState *ls, SemInfo *seminfo)** 函数处.<br>
这个函数的主体是 switch 结构, 我们向下翻找到它的 **default** 部分.

```cpp
default: {
if (lislalpha(ls->current)) {  /* identifier or reserved word? */
  TString *ts;
  do {
    save_and_next(ls);
  } while (lislalnum(ls->current));

// 将上面代码改成下面的
default: {
if (lislalpha(ls->current) || ls->current == '_' ||  ls->current > 0x80) {  /* identifier or reserved word? */
  TString *ts;
  do {
    save_and_next(ls);
  } while (lislalnum(ls->current) || ls->current == '_' ||  ls->current > 0x80);
```

现在开始来**编译lua解释器(lua.exe).**

打开 VS2013 新建一个 win32 控制台程序, 取名为 Lua, 不要勾选预编译头.

![](http://i57.tinypic.com/dwc479.jpg)

删除 VS2013 帮我们自动生成的 Lua.cpp.

![](http://i57.tinypic.com/2evf6g9.jpg)

接着将 **src** 文件夹下源代码文件复制到工作目录下, 然后在刚新建的工程中添加**除 luac.c 外**的其他文件.<br>
打开属性编辑框, 在工程的预处理器设置中添加一行 **_CRT_SECURE_NO_WARNINGS** (屏蔽 C4996 错误).

![](http://i60.tinypic.com/332qpgg.jpg)

最好也设置下程序语言集, 因为 Lua 的源代码都是基于 ASCII 的, 例如:

```java
char buff[4*sizeof(void *) + 8]; /* should be enough space for a '%p' */
int l = sprintf(buff, "%p", va_arg(argp, void *));
```

![](http://i61.tinypic.com/2dryiv5.jpg)

开始 **Release 版本编译**, 就得到我们的lua解释器(lua.exe)了, 将它复制出来保存好.

接下来开始**编译lua编译器(luac.exe)**.

移除 VS2013 工程中之前添加的 lua.c 文件, 添加进 luac.c 文件, 然后再编译, 将编译出来后的 lua.exe 重命名为 luac.exe, 复制出来保存.

接下来开始**编译lua的库文件(*.lib),** 移除 VS2013 工程中刚添加的 luac.c 文件, 打开属性编辑框, 修改配置类为静态库(.lib).

![](http://i62.tinypic.com/141jbdt.jpg)

接下开始编译.

注意: lua 的库文件需要编译 Release 和 Debug 两个版本, 另外, 两个版本都要设置下预处理器设置和配置类型.

此时3部分内容都编译完毕:

![](http://i59.tinypic.com/2ezln5l.jpg)

我们已经发现到:<br>
其实最关键就是除开不必要的文件;<br>
编译 lua（lua.exe，解析器） 时删除 luac.c，加入 lua.c;<br>
编译 luac（luac.exe，编译器）时删除 lua.c，加入 luac.c;<br>
编译 lib（lua.dll，库）时把 lua.c 和 luac.c 都删除.

* * *

将下来**设置 Sublime Text, 让它可以运行 lua 命令.**

[Sublime Text 优化版本下载](http://hrtsea.com/2014/09/14/sublime-text/)

Tool(工具)->Build System(编译系统) -> New Build System(新编译系统), 添加如下代码:

```js
{
 "cmd": ["C:/Users/Administrator/Desktop/lua/Release/lua.exe", "$file"],
 "file_regex": "^(?:lua:)?[\t](...*?):([0-9]*):?([0-9]*)",
 "selector": "source.lua"
}
```

保存为 lua.sublime-build 就可以了, 此时 Sublime Text 的编译选项就多出来了 lua.

编辑好 lua 脚本后, 按 ctrl + b, 或者 工具 –> 立即编译 就得到脚本的运行结果了.

![](http://i62.tinypic.com/2n8r3uq.jpg)

![](http://i59.tinypic.com/2iwaz53.jpg)

* * *

为了方便以后 C 与 Lua 交互, 整合一下交互开发环境.

建立文件夹 LuaScript, 在 LuaScript 中再创建文件夹 lib 和 include, 将刚才编译的 Lua_Debug.lib 和 Lua_Release.lib 文件添加到 lib 文件夹下, 将 src 文件夹下的** lauxlib.h、lua.h、****luaconf****.h、lualib.h **四个头文件加入到 include 文件夹下.
在 LuaScript 文件夹里再创建一个 LuaEx.h 文件, 添加以下代码:

```java
#pragma once

extern "C" {
#include "lua.h"
#include "lauxlib.h"
#include "lualib.h"
#include "luaconf.h"
}

#ifdef _DEBUG
#pragma comment(lib,"Lua_Debug.lib")
#else
#pragma comment(lib,"Lua_Release.lib")
#endif
```

以后, 如果 vs2013 中要用到 lua, 就将 LuaScript 文件夹复制到工程目录下, 然后设置当前工程的包含目录和库目录, 再在需要的地方 #include "LuaEx.h" 就可以了.

![](http://i62.tinypic.com/29bev89.jpg)

![](http://i59.tinypic.com/2csb4n4.jpg)

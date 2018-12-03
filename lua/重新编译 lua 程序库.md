通过上二篇的介绍, 已经可以在 C/C++ 程序中简单的使用 Lua 脚本, 然而那只是简单地在 VC 程序中直接访问 Lua API.<br>
对于小项目来说这是可以的, 但更好的方式是抽象出简单的 Lua 程序库, 让它整合 C/C++ 和 Lua API 代码, 让其可以复用在多个项目中.<br>
不是有首程序员版的<双截棍>吗, 里面有句歌词是这样的: **一个优秀的库函数, 一用好多年, 拷贝好带身边**.<br>
接下来就开始创建这个程序库.

同第一篇一样, 采用 VS2013 + lua-5.2.4 来进行编译, 先去 Lua 官网下载 Lua 源码: [http://www.lua.org](http://www.lua.org)<br>
然后修改源码, 使 Lua 支持中文的变量名及函数名.<br>
接下来打开 VS2013 新建一个 win32 静态链接库 程序, 取名为 LuaLib, 不要勾选预编译头.<br>
将 src 整个文件夹复制到工作目录下(注意这里是连 src 文件夹一起复制的, 第一篇中是复制的源码), 然后在刚新建的工程中添加**除 luac.c 和 lua.c 外**的其他源代码文件.

![](http://i62.tinypic.com/6r0n79.jpg)

打开属性编辑框, 在工程的预处理器设置中添加一行 \_CRT\_SECURE\_NO\_WARNINGS, 并且设置当前工程的包含目录.

![](http://i62.tinypic.com/b8nv55.jpg)

![](http://i58.tinypic.com/awauy1.jpg)

将下来为工程创建一个 CLua 类([源码传送门](https://github.com/uldaman/LuaLib)), 该类封装了一部分 Lua API 函数, 隐藏了 Lua 需要的初始化和关闭处理, 它的使用说明及源码分析将在后面进一步说明.

好, 接下来就开始编译 LuaLib 工程的 Debug 和 Release 版本, 将生成的 .lib 文件分别命名为 LuaLib\_Debug.lib 和 LuaLib\_Release.lib.

现在把 CLua.h 单独复制出来, 在文件开头添加如下代码:

```cpp
#ifdef _DEBUG
#pragma comment(lib,"LuaLib_Debug.lib")
#else
#pragma comment(lib,"LuaLib_Release.lib")
#endif
```

好, 现在的 CLua.h 中的代码如下:

```cpp
#pragma once

#ifdef _DEBUG
#pragma comment(lib,"LuaLib_Debug.lib")
#else
#pragma comment(lib,"LuaLib_Release.lib")
#endif

struct lua_State;

#define LuaGlue extern "C" int// 定义 LuaGlue 宏

extern "C" { // 定义一个 C 方式的 LuaFunctionType 类型函数指针
  typedef int(*LuaFunctionType)(lua_State *pLuaState);
};

class cLua {
public:
  cLua();
  virtual ~cLua();

  bool        RunScript(const char* pFilename);
  bool        RunString(const char* pCommand);
  const char* GetErrorString(void);

  bool        AddFunction(const char* pFunctionName, LuaFunctionType pFunction);

  const char* GetStringArgument(int num, const char* pDefault = NULL);
  double        GetNumberArgument(int num, double dDefault = 0.0);
  int         GetIntArgument(int num, int nDefault = 0);

  void        PushString(const char* pString);
  void        PushNumber(double value);
  void        PushInt(int value);

  void        SetErrorHandler(void(*pErrHandler)(const char* pError));

  lua_State*  GetScriptContext(void);

private:
  lua_State*  m_pScriptContext;
  void(*m_pErrorHandler)(const char *pError);
};
```

此时, 我们新的 Lua 程序库就制作完毕了, 使用时只要将 刚修改的 **CLua.h**、**LuaLib\_Debug.lib** 和 **LuaLib\_Release.lib** 三份文件复制到工程目录下, 然后在合适的地方导入头文件 #include "CLua.h" 就可以了.

* * *

接下来使用 CLua 来实现第二篇中的例子.<br>
打开 VS2013 新创建一个 win32 应用程序 工程, 将 CLua.h、LuaLib\_Debug.lib 和 LuaLib\_Release.lib 三份文件拷贝过去.<br>
修改源码如下:

```cpp
#include <windows.h>
#include "CLua.h"


LuaGlue LuaGule_Out(lua_State* L) {
  printf("This is a LuaGule! \r\n");
  return 0;
}

int _tmain(int argc, _TCHAR* argv[]) {
  cLua lua;
  lua.AddFunction("新函数", LuaGule_Out);
  lua.RunScript("C:/Users/Administrator/Desktop/test.lua");

  system("PAUSE");
  return 0;
}
```

可以看到, 相对第二篇中的程序, 新代码中的主要变化有:<br>
\*. 主程序中不再有初始化和关闭 Lua 环境的代码;<br>
\*. LuaGlue 函数的定义因为使用了 LuaGlue 宏而简化了;<br>
\*. 注册 LuaGlue 函数的 Lua API 被 CLua::AddFunction 代替;<br>
\*. 执行 Lua 脚本的 Lua API 被 CLua::RunScript 代替.

# vs 环境设置

在正式开始前, 有必要先设置下 VS 的开发环境, 之前我就是因为没设置好, 导致生成的 dll 注入后各种问题, 折腾了一天才发现是依赖库没设置对…所以单独拿一篇笔记来记下这个.

首先是 Debug 和 Release 版本通用设置:

## [包含目录] 和 [库目录] 的设置

![](http://i62.tinypic.com/t69mvt.jpg)

包含目录要把 D3D9 和 CEGUI 的路径加进去, 我这里都是安装在 E 盘:

- E:\Microsoft DirectX SDK (June 2010)\Include;
- E:\cegui-0.8.4\dependencies\include;
- E:\cegui-0.8.4\cegui\include;

库目录也这么设置:

- E:\Microsoft DirectX SDK (June 2010)\Lib\x86;
- E:\cegui-0.8.4\dependencies\lib;
- E:\cegui-0.8.4\bin;

## 设置[字符集]

![](http://i61.tinypic.com/35858j7.jpg)
设置成 [使用多字节字符集]

## 设置 C++ 异常

![](http://i58.tinypic.com/3007ha8.jpg)
设置成 [是, 但有 SEH 异常(/EHa)]

## 取消 预编译头

![](http://i58.tinypic.com/11w3p20.jpg)

以上是需要 Debug 和 Release 分别设置一次的, 都这么设置一次就可以了, 接下来是**两个版本不同的设置**部分, 即 [**附加依赖项**] 的设置:

![](http://i59.tinypic.com/28vzxfr.jpg)

**Debug 版本:**

- dynamic\freetype_d.lib
- dynamic\pcre_d.lib
- E:\cegui-0.8.4\lib\CEGUIBase-0_d.lib
- winmm.lib
- DbgHelp.lib
- E:\cegui-0.8.4\lib\CEGUIDirect3D9Renderer-0_d.lib

**Release 版本:**

- dynamic\freetype.lib
- dynamic\pcre.lib
- E:\cegui-0.8.4\lib\CEGUIBase-0.lib
- winmm.lib
- DbgHelp.lib
- E:\cegui-0.8.4\lib\CEGUIDirect3D9Renderer-0.lib

可以看到, Debug 使用的是 xxx_d.dll, 而 Release 使用的是 xxx.dll.

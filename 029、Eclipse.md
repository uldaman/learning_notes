## 安装
在官网上下载最新版本的 Eclipse, 不过它是英文版的, 我们需要手动下载语言包, Eclipse 有一个语言包项目, 叫 Eclipse Babel Project, 如果需要语言包, 可以联机从这儿下载.

[http://www.eclipse.org/babel/](http://www.eclipse.org/babel/)

找到下载页面后, 还要根据自己使用的 Eclipse 的版本来确定在哪下载.

![](http://i59.tinypic.com/2ibyxyx.jpg)

![](http://i58.tinypic.com/30sfqyb.jpg)

可以看到我的是 Luna 版本.

点击第一张图上的 Luna, 跳转到语言包下载界面, 找到中文包, 下载, 解压, 覆盖即可.

![](http://i57.tinypic.com/fn8sg0.jpg)

再次打开 Eclipse 就是中文的了.

## 使用
用 Eclipse 编写 Java 程序可以总结为四步:

1. 创建 Java 项目
2. 创建程序包
3. 编写 Java 源代码
4. 运行 Java 程序

### 设置字体
"窗口" -> "首选项"

然后在左上角的过滤器中输入外观, 选择 "颜色和字体", 在右边选择 "基本" -> "文本字体" -> "编辑"

![](http://i67.tinypic.com/op8do8.jpg)

### 自动提示
接下来, 设置下 eclipse 的自动提示<br>(你可以在任何时候 alt + / 来触发).
window –> Preferences –> Java –> Editor –> Content Assist:

![](http://i60.tinypic.com/1zoj2no.jpg)

__abcdefghijklmnopqrstuvwxyz.__(注意后面的点)

这样改完后会有个问题, 就是当按下 "空格键"、"=" 时会上屏候选列表, 按习惯我们可改成 "Tab"、"Enter" 上屏.

首先, 最好下载一个 Eclipse RCP 版本 (该版本修改源代码比较方便，能自动导入源代码):

![](http://i66.tinypic.com/2aeytts.jpg)

打开 window \-\> show view, 选择 Plug\-ins (如果没有的话, 在 show view 的 other 里找找), 再找到 `org.eclipse.jface.text`, 右键单击, 选择 import as \-\> Source Project, 导入完成后, 在 Eclipse 的 workspace 就可以看到这个 project 了.

![](http://i67.tinypic.com/ok94jt.jpg)

![](http://i67.tinypic.com/2nv79e9.jpg)

![](http://i63.tinypic.com/53nvrd.jpg)

打开 `org.eclipse.jface.text.contentassist.CompletionProposalPopup` 文件第 1336 行代码, 具体代码如下:

![](http://i67.tinypic.com/20rae14.jpg)

```java
if (contains(triggers, key)) {
                        e.doit= false;
                        hide();
                        insertProposal(p, key, e.stateMask, fContentAssistSubjectControlAdapter.getSelectedRange().x);
                    }
```

修改之后的代码是:

```java
if (key != '=' && key != 0x20 && key != ';' && contains(triggers, key)) {
                        e.doit= false;
                        hide();
                        insertProposal(p, key, e.stateMask, fContentAssistSubjectControlAdapter.getSelectedRange().x);
                    }
```

如果想要使用 "tab" 键上屏需要修改 1328 行, 具体代码如下:

```java
case '/t':
e.doit= false;
fProposalShell.setFocus();
return false;
```

修改之后的代码是:

```java
case '\t':
e.doit= false;
insertSelectedProposalWithMask(e.stateMask);
break;
```

修改完成之后需要保存替换原来的 jar 文件:

项目右键 \-\> export

![](http://i67.tinypic.com/2pydoci.jpg)

选择导出方式如下图:

![](http://i64.tinypic.com/2yynhid.jpg)

点击 Next, 选择 Destination 选项卡, 选择 Directory, 选择一个要保存插件的目录, 然后 Finish.

![](http://i63.tinypic.com/2vacpe1.jpg)

然后就会在所选的目录下产生一个新的 plugins 目录, 里面有一个 jar 文件, 用它替换掉 Eclipse/plugins 里面的 (注意版本号可能会不一样), 最后重新启动一下 Eclipse.

### 导航器
类似 "资源管理器", 它的视图和 "包资源管理器" 有点像, 因为有些文件或文件夹在 "包资源管理器" 看不到, 这时可以到 "导航器" 中查看.

"窗口" -> "显示视图" -> "其他", 在弹出窗中选择 "常规" -> "导航器", 此时 "导航器" 就出现在 IDE 左侧的视图中了.

### 自动构建
eclipse 的 java 构建器包含了两种构建 java 程序的方法:

- 自动构建
- 手动构建

其中, 自动构建是 java 构建器默认采用的方式, 即当文件修改后保存或者对整个项目进行刷新时, java 构建器就会对其进行编译.

这显然不是一种我希望的方式, 我希望是我点击 "Run" 或者 "Debug" 时 IDE 再去构建, 而不是保存一下文件就构建一下.

关闭方法很简单, "项目" -> "自动构建", 取消 "自动构建" 前面的选项即可.

### 抽取函数
快捷键 `Shift + Alt + M` 能将选中的代码块进行方法抽取.

这是属于 **重构** 功能的一部分, 可以点击选中的代码块, 选择 "重构" \-\> "抽取方法"

### 设置编码
统一设置为 __utf-8__ 编码, Window -> Preferences -> General -> Workspace -> Text file encoding -> Other -> UTF-8.

统一设置为 __utf-8__ 编码, Window -> Preferences -> General -> Content Types, 在右面选择 "Text", 在 default encoding 输入 "UTF-8", 点 "update" 按钮更新.

### 设置 Monokai 主题
#### 配置黑色框架
Window \-\> Preferences \-\> General \-\> Apperance 选择主题为 Dark, 确认.

![](http://i68.tinypic.com/30hnd45.jpg)

完成之后确认, 颜色大概是这个样子:

![](http://i66.tinypic.com/jb74aw.jpg)

这肯定不是我们想要的样子, 因为这个主题只是配置了框架的颜色，代码编辑区的样式还需要我们继续配置.

### 配置代码编辑区的样式
去 帮助 \-\> 安装新软件，按下 "添加站点进入", 输入 `http://eclipse-color-theme.github.io/update` 作为网址, 然后点击继续安装.

![](http://i64.tinypic.com/2u7c7ro.jpg)

安装完成之后, Window \-\> Preferences \-\> General \-\> Appereance \-\> Color Theme 选择其中的样式.

![](http://i63.tinypic.com/20ppxrn.jpg)

## 常用快捷键
0. Ctrl + 1 (快速修复)
1. Ctrl + 鼠标, 跳转到鼠标点击处 api 的源码文件
2. Ctrl + Shift + T 查找类文件 (包括源码文件)
3. Ctrl + D (删除当前行)
4. Ctrl + Alt + ↓ (复制当前行到下一行)
5. Alt + / 或者说是 Ctrl + 空格 (由于后者与输入法的快捷键冲突, 所以, 我一般都用前者) 作用：快速插入
6. Alt + Shift + R 重命名非常好用
7. Ctrl + Q 定位到最后编辑的地方
8. Ctrl + Shift + O (自动导入包)
9. Ctrl + / 注释当前行,再按则取消注释
10. ALT + F11 切换全屏模式
11. Alt + Shift + Y 切换自动换行模式

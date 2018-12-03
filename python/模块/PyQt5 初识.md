# 环境
* Mac 10.13
* Python 3.6, `brew install python`, Eric6 最新的中文版是 17.12, 而 17.12 只支持到 python 3.6
* Qt Creator 5.11, 使用 Online 安装, 离线安装会找不到 Qt Designer
* SIP, `pip3 install PyQt5-sip`, Python 调用 C/C++ 库的必备模块
* QScintilla, `pip3 install QScintilla`, Scintillar 源代码编辑控件的 Qt 接口
* PyQt5, `pip3 install PyQt5`
* [Eric6 17.12](https://sourceforge.net/projects/eric-ide/files/eric6/stable/17.12/)
    - `python3 install.py`
    - Interface -> Interface, Language 选择中文, 重启生效

# 配置 Eric6
`setting -> Preferences`

## Qt Tools
用来支持编辑 Qt UI 文件.

![](http://i63.tinypic.com/1fcjna.jpg)

## PyQt Docs
以前通过 pip 安装 PyQt 后, 帮助文档会被离线下载到本地, 新版本好像已经没有这种操作了, 只能在线:

`http://pyqt.sourceforge.net/Docs/PyQt5/modules.html`

![](http://i68.tinypic.com/1tu8sz.jpg)

## 代码提示
![](http://i67.tinypic.com/33pfyhs.jpg)

![](http://i64.tinypic.com/28k6lbr.jpg)

## 自动完成
![](http://i68.tinypic.com/b3wrvn.jpg)

![](http://i66.tinypic.com/2qm0js9.jpg)

![](http://i64.tinypic.com/opobjc.jpg)

![](http://i65.tinypic.com/2aj42zd.jpg)

![](http://i67.tinypic.com/35d2d53.jpg)

## 等宽字体
推荐 [Microsoft YaHei Mono](https://pan.baidu.com/s/1ErAw2sTNW5wddaf6BpfCYg)

![](http://i64.tinypic.com/6gdhyw.jpg)

## 语法高亮
![](http://i68.tinypic.com/2nkuaz6.jpg)

# 主要模块
| 模块            | 功能                                                                                                                  |
| --------------- | --------------------------------------------------------------------------------------------------------------------- |
| QtCore          | 核心的非GUI的功能. 主要和时间、文件与文件夹、各种数据、流、URLs、mime类文件、进程与线程一起使用.                      |
| QtGui           | 窗口系统、事件处理、2D图像、基本绘画、字体和文字的模块.                                                               |
| QtWidgets       | 一系列创建桌面应用的UI元素.                                                                                           |
| QtMultimedia    | 处理多媒体的内容和调用摄像头API的模块.                                                                                |
| QtBluetooth     | 查找和连接蓝牙的模块.                                                                                                 |
| QtNetwork       | 网络编程的模块, 这些工具能让TCP / IP和UDP开发变得更加方便和可靠.                                                      |
| QtPositioning   | 定位的模块, 可以使用卫星、WiFi甚至文本.                                                                               |
| QtWebSockets    | WebSocket协议的模块.                                                                                                  |
| QtWebKit        | 一个基WebKit2的web浏览器.                                                                                             |
| QtWebKitWidgets | 基于QtWidgets的WebKit1的模块.                                                                                         |
| QtXml           | 处理xml的模块, 提供了SAX和DOM API的模块.                                                                              |
| QtSvg           | 显示SVG内容的模块, Scalable Vector Graphics(SVG) 是一种是一种基于可扩展标记语言(XML), 用于描述二维矢量图形的图形格式. |
| QtSql           | 处理数据库的模块.                                                                                                     |
| QtTest          | 测试PyQt5应用.                                                                                                        |

# Hello World
新建项目

![](http://i64.tinypic.com/2zpt5b4.jpg)

配置项目属性

![](http://i64.tinypic.com/2nkqpkz.jpg)

新建 UI (切换到下图序号 1 的标签, 在下面空白区右键, 选择新建窗体)

![](http://i68.tinypic.com/3voxt.jpg)

![](http://i67.tinypic.com/91m1jo.jpg)

![](http://i64.tinypic.com/240x5b7.jpg)

点击保存后, eric6 上出现了 `first_window.ui` 文件, 并且自动打开了 `Qt 设计师`.

![](http://i66.tinypic.com/1phydz.jpg)

这个 `first_window.ui` 文件是 Qt 专用的, python 不认识它, 但是最终我们是通过 python 来运行程序, 所以需要把 `*.ui` 文件转化成 python 识别的格式, 方法很简单, 在 `*.ui` 文件上右键选择 `编译窗体` (编译成功/失败会弹框提示).

![](http://i66.tinypic.com/sm3bte.jpg)

切换回下图序号 1 的标签, 可以看到多了一个 `Ui_first_window.py` 文件 (以 **Ui** 开头, 标明这是从 Qt 的 Ui 文件转换来的)

![](http://i68.tinypic.com/11qr6op.jpg)

这个文件有自测代码, 在 Eric 上按 **F5** 或者点击下图中的图标, 就能打开刚才新建的窗体了.

![](http://i65.tinypic.com/dfitfn.jpg)

![](http://i65.tinypic.com/292v8dy.jpg)

虽然 `Ui_first_window.py` 能够被直接运行, 但它本质还是个 ui 层面的文件 (从它的命名就可以看出), 不应该被直接运行, 而从 OOA/D 的角度说, 它是属于 view 层,  eric 提供了快捷的方法从 `Ui_xxx.py` 文件生成 controller 层的代码, 所有的在界面上的事件处理都在这层完成.

首先切换到下图序号 1 的标签, 在 `first_window.ui` 上右键, 选择 "生成对话框代码"

![](http://i65.tinypic.com/2qs5wmo.jpg)

![](http://i66.tinypic.com/aui5wp.jpg)

切换到下图序号 1 的标签, 可以看到又多了个文件

![](http://i67.tinypic.com/1411mcn.jpg)

这个文件要做些修改.

去掉一个多余的点，将

```python
from .Ui_first_window import Ui_Form
```

变成

```python
from Ui_first_window import Ui_Form
```

添加测试代码:

```python
if __name__ == "__main__":
    import sys
    from PyQt5.QtWidgets import QApplication
    app = QApplication(sys.argv)
    dlg = Dialog()
    dlg.show()
    sys.exit(app.exec_())
```

然后就可以按 **F5** 或者 点击工具栏上的图标, 就能打开刚才新建的窗体了.

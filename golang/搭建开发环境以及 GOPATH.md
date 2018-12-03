#1. golang 安装
地址: [https://golang.org/dl/](https://golang.org/dl/), 去下载合适的版本, 直接安装即可, 安装完成后, 检查下 bin 目录是否被添加到系统环境变量了.

#2. 开发工具安装
开发工具可以选择 LiteIDE 或者 安装了 GoClipse 插件的 Eclipse.

这里我选择的是 **LiteIDE**, 下载地址: [http://sourceforge.net/projects/liteide/](http://sourceforge.net/projects/liteide/)

然后还要安装个 **gcc**, 下载地址: [http://www.pc6.com/softview/SoftView_110479.html](http://www.pc6.com/softview/SoftView_110479.html), 也可以直接搜索  gcc for windows 自行下载.

解压 **LiteIDE** 压缩包, 运行 bin 目录下的 **liteide.exe**.

##设置当前系统
![win 64](http://i64.tinypic.com/10fxtg8.jpg)

我这里选择的是 **win 64**.

##设置 go 环境
查看\-\>编辑当前环境, 注意红框里的内容.

![环境变量](http://i63.tinypic.com/2rd7wgl.jpg)

##设置字体/缩进
查看\-\>选项\-\>LiteEditor

![缩进](http://www.smallcpp.cn/theme/images/Golang/缩进.png)

##设置 GOPATH
当编译器查找包时, 就会到 GOROOT 和 GOPATH 环境变量引用的位置去查找 (实际是 `GOROOT/src` 和 `GOPATH/src`).

查看->管理GOPATH, 创建新目录 G:\GoProject, 并添加到 "自定义目录".

![GOPATH](http://i66.tinypic.com/iopxyp.jpg)

**应该**为每个工程设置不同的 GOPATH, 以保持源代码和依赖的隔离.

每个 GOPATH 目录由 `bin`、`pkg`、`src` 三个子目录组成:

- bin: `go install` 安装可执行文件目录
- pkg: `go install` 安装静态库 (.a) 目录
- src: 项目源码目录

注意, go 的编译都是静态编译, `bin` 目录中编译出来的可执行文件不需要 `pgk` 目录中的 `.a` 库文件就可以直接执行.

那么 `.a` 库文件有个什么用? 是不是像 dll 一样, 直接把 `.a` 库文件给用户, 然后提供个 API 文档, 但是不提供源码, 用户就直接可以用了呢?

很不幸, 不可以, 必须有源码. `.a` 库文件对于用户来说, 是没有用的, 编译器用的; Go 在编译的时候先判断 `pkg` 目录中 `.a` 包的源码是否有改动, 如果没有的话, 就不再重新编译, 这样可以加快速度.

另外, 使用 `go build` 编译时, 也会生成临时的 `.a` 库文件, 当然, 我们无需关注, 知道一下即可.

#3. 测试
##文件->新建
选择上一步添加的 **GOPATH**, 模板为 **Go1 Command Project**

![新建](http://i68.tinypic.com/122koxf.jpg)

然后, LiteIDE 会自动给你生成个 main.io 文件, 内容如下:

```go
// test project main.go
package main

import (
    "fmt"
)

func main() {
    fmt.Println("Hello World!")
}
```

##编译->编译配置
在 **BUILDARGS** 添加 **-gcflags "-N -l"**, 目的是去掉编译优化, 方便调试.

![编译设置](http://i65.tinypic.com/vskpyq.jpg)

点击按钮 `BR`, 或者 `FR` 编译并运行程序.

![调试](http://i63.tinypic.com/2rvzslu.jpg)

##开始调试
![断点](http://www.smallcpp.cn/theme/images/Golang/断点.png)

点击上图所示按钮, 或按键盘 `F9` 可设置断点, `F5` 开始调试程序.

启动调试后, 程序会断在 `main` 函数第一行, 然后可以如下图所示进行控制:

![调试](http://www.smallcpp.cn/theme/images/Golang/调试.png)

我们通过一个命令行版本的计算器程序来体验下如何用 LiteIDE 进行 Go 开发.

打开 LiteIDE, 点击新建

![新建 calc](http://i66.tinypic.com/24vutn4.jpg)

在 main.go 中输入如下代码:

```go
// clac project main.go
package main

import (
    "fmt"
    "os"    // 用来取命令行参数
    "simplemath"    // 我们自定义的包, 里面是 add 算法
    "strconv"    // 用来做字符串转换
)

var Usage = func() {
    fmt.Println("USAGE: calc command [arguments]...")
    fmt.Println("\nThe commands are:\n\tadd\tAddition of two values.")
}

func main() {
    args := os.Args    // := 表示同时进行变量声明和初始化的工作
    if args == nil || len(args) < 3 {
        Usage()
        return
    }

    switch args[1] {
    case "add":
        if len(args) != 4 {
            fmt.Println("USAGE: calc add <integer1> <integer2>")
            return
        }
        v1, err1 := strconv.Atoi(args[2])
        v2, err2 := strconv.Atoi(args[3])
        if err1 != nil || err2 != nil {
            fmt.Println("USAGE: calc add <integer1> <integer2>")
            return
        }
        ret := simplemath.Add(v1, v2)
        fmt.Println("Result: ", ret)
    default:
        Usage()
    }
}
```

然后, 在 src 目录下 (与 calc 目录同级) 新建个 **simplemath** 目录, 并新建个 add.go 文件:

```go
package simplemath

func Add(a int, b int) int {
    return a + b
}
```

此时的文件结构如下:

![文件结构](http://i68.tinypic.com/2r71y04.jpg)

点击 LiteIDE 工具栏上的 **B** 图标进行编译, 然后 calc 目录下就生成了一个 exe 可执行文件, 打开 cmd 测试下:

![命令行](http://i63.tinypic.com/2ztem21.jpg)

另外, LiteIDE 支持__单元测试__, 我们在写 simplemath 包时, 可以为 add 函数编写了对应的单元测试代码.

再新建个 add_test.go 文件:

```go
package simplemath

import "testing"

func TestAdd1(t *testing.T) {
    r := Add(1, 2)
    if r != 3 {
        t.Errorf("Add(1, 2) failed. Got %d, expected 3.", r)
    }
}
```

点击 LiteIDE 工具栏上的 **T** 图标就能执行该测试单元了.

![测试单元](http://i65.tinypic.com/347fuz6.jpg)

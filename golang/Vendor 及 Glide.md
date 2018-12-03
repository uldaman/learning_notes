# Vendor
Vendor 是 Go 1.5 之后新添加的依赖目录. 1.5 时需要设置 `GO15VENDOREXPERIMENT=1`, 而 1.6 之后 Vendor 则直接可用.

Vendor 依然要位于 `GOPATH/src` 目录之下, 当查找依赖包路径时, 搜索顺序如下：

1. 当前包下的 vendor 目录
2. 向上级寻找 src/vendor 目录
3. GROOT 及 GOPATH

那么为什么会需要 Vendor?

其中一个答案是为了使用不同版本的 package. 举例, 现在有个项目, 里面有两个 package 都依赖另一个 package, 此时只要把这个依赖 package 放到 GOPATH/src 下就可以了; 但如果这两个 package 依赖的 package 版本并不一样呢? 那么统一的 GOPATH 显然不能解决这个问题, vendor 就可以出场了.

# Glide
Glide 则是和 Vendor 配合非常好的包管理解决方案, 它会帮我们在 Vendor 目录下管理 `glide.yml` 文件中指定的依赖 package.

如果被依赖的 package 还依赖着其它 package, Glide 也会递归获取它们, 并将它们平展开存放在最开始的 vendor 目录下 (这样其实是有问题的, 比如 A depend B and C, B depend C, 这样 C 就被依赖了两次...).

> 应将 `glide.yaml` 和 `glide.lock` 进行版本控制, 而将 `vendor` 目录忽略掉.

更多使用参考: [Golang 包管理](https://deepzz.com/search.html?q=tag:golang%E5%8C%85%E7%AE%A1%E7%90%86)

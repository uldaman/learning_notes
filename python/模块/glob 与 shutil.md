glob 模块是最简单的模块之一, 内容非常少, 用它可以查找符合特定规则的文件路径名, 就像在 windows 下使用文件搜索功能一样, 查找文件只用到三个匹配符: `*`, `?`, `[]`, `*` 匹配 0 个或多个字符, `?` 匹配单个字符, `[]` 匹配指定范围内的字符 (如 [0-9]匹配数字).

[TOC]

# glob
## glob.glob
返回所有匹配的文件路径列表, 它只有一个参数 pathname, 定义了文件路径匹配规则, 这里可以是绝对路径, 也可以是相对路径, 下面是使用 glob.glob 的例子:

```python
import glob

# 获取指定目录下的所有图片
print glob.glob(r"E:\Picture\*\*.jpg")

# 获取上级目录的所有.py文件
print glob.glob(r'../*.py')  # 相对路径
```

## glob.iglob
与 glob.glob 类似, 但它获取的是一个可迭代对象, 使用它可以逐个获取匹配的文件路径名 (类似 rang 与 xrang 的区别).

下面是一个简单的例子:

```python
import glob
# 父目录中的.py文件
f = glob.iglob(r'../*.py')
print f  # <generator object iglob at 0x00B9FF80>

for py in f:
    print py
```

# shutil
High-level file operations  高级的文件操作模块, 提供了一些易操作的文件**移动**、**复制**、**打包**、**压缩**、**解压**API.

| API                                                                                                                           | 功能                                                                                                                                                                |
| ----------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| copyfileobj(fsrc, fdst[, length=16*1024])                                                                                     | copy 文件内容到另一个文件, 可以 copy 指定大小的内容                                                                                                                 |
| copyfile(src, dst)                                                                                                            | copy 文件内容, 是不是感觉上面的文件复制很麻烦? 还需要自己手动用 open 函数打开文件, 在这里就不需要了, 事实上, copyfile 调用了 copyfileobj                            |
| copymode(src,dst)                                                                                                             | 仅 copy 权限, 不更改文件内容, 组和用户                                                                                                                              |
| copystat(src,dst)                                                                                                             | 复制所有的状态信息, 包括权限, 组, 用户, 时间等                                                                                                                      |
| copy(src,dst)                                                                                                                 | 复制文件的内容以及权限, 先 copyfile 后 copymode                                                                                                                     |
| copy2(src,dst)                                                                                                                | 复制文件的内容以及文件的所有状态信息, 先 copyfile 后 copystat                                                                                                       |
| copytree(src, dst, symlinks=False, ignore=None, copy\_function=copy2,ignore\_dangling\_symlinks=False)                        | 递归的复制文件内容及状态信息                                                                                                                                        |
| rmtree(path, ignore\_errors=False, onerror=None)                                                                              | 递归地删除文件                                                                                                                                                      |
| move(src, dst)                                                                                                                | 递归的移动文件                                                                                                                                                      |
| make\_archive(base\_name, format, root\_dir=None, base\_dir=None, verbose=0, dry\_run=0, owner=None, group=None, logger=None) | 压缩打包<br>base\_name: 压缩打包后的文件名或者路径名<br>format: 压缩或者打包格式 "zip"、"tar"、"bztar"、"gztar"<br>root\_dir: 将哪个目录或者文件打包 (也就是源文件) |

# 1. 概述

Redis 从 2.6 版本开始在服务器内部嵌入了一个 Lua 解释器, 使得用户在服务器端执行 Lua 脚本.

- 使用脚本可以直接在服务器端执行 Redis 命令, 并且一般的数据处理操作可以直接使用 Lua 脚本来完成 (不必再先从服务端查询数据, 然后让客户端进行处理, 最后又返回给服务端), 相当于为 Redis __自定义了新"命令"__

- 所有脚本都是以__事务__的形式来执行的, 脚本在执行过程中不会被其他工作打断, 也不会引起任何竞争条件, 完全可以使用 Lua 脚本来代替事务

- 所有脚本都是__可重用__的, 也即是说, 重复执行相同的操作时, 只要调用储存在服务器内部的脚本缓存就可以了, 不用重新发送整个脚本, 从而尽可能地节约网络资源

__需求分析:__<br>
我们希望的 Redis 提供一个 "RATELIM ITING" 命令用来实现访问频率限制功能, 这个命令只需要我们提供键名、时间限制和在时间限制内最多访问的次数三个参数就可以直接返回访问频率是否超限!

然而可惜的是 Redis 并没有提供这个命令, 不过我们可以使用 Redis 脚本功能自己定义新的命令, 代码如下:

```lua
local times = redis.call('incr', KEYS[1])
if times == 1 then
    -- KEYS[1]键刚创建, 所以为其设置生存时间
    redis.call('expire', KEYS[1], ARGV[1])
end

if times ＞ tonumber(ARGV[2]) then
    return 0
end

return 1
```

<br>
这个功能就已经实现了..., 先不分析具体代码, 看看怎么使用它.

首先把这段代码存为 __ratelimiting.lua__, 然后在命名行中输入:

```
redis-cli --eval /path/ratelimiting.lua rateKey , 10 3
```

其中 __--eval__ 参数是告诉 redis-cli 读取并运行后面的 Lua 脚本, __/path/ratelimiting.lua__ 是 ratelimiting.lua 文件的位置, 后面跟着的是传给 Lua 脚本的参数, 其中 "," 前的 __rate.Key__ 是要操作的键, 可以在脚本中使用 __KEYS[1]__ 获取, "," 后面的 10 和 3 是参数, 在脚本中能够使用 __ARGV[1] __和 __ARGV[2]__ 获得.

结合脚本的内容可知这行命令的作用就是将访问频率限制为每 10 秒最多 3 次, 所以在终端中不断地运行此命令会发现当访问频率在 10 秒内小于或等于 3 次时返回 1, 否则返回 0.

__注意__ 上面的命令中 "," 两边的空格不能省略, 否则会出错.

# 2. 语法说明
## 2.1 Lua 调用 Redis 命令
可以使用 __redis.call()__ 及 __redis.pcall()__ 调用 Redis 命令, 就像这样:

```lua
redis.call('set', 'foo', 'bar')
local value = redis.call('get', 'foo') -- value 的值为 bar
```

redis.call 函数的返回值就是 Redis 命令的执行结果.

__redis.call()__ 和 __redis.pcall()__ 的区别:<br>
功能相同, 唯一的区别是当命令执行出错时 __redis.pcall()__ 会记录错误并继续执行, 而 __redis.call()__ 及 __redis.pcall()__ 会直接返回错误, 不会继续执行.

Redis 返回值类型 和 Lua 数据类型__转换规则__

| Redis 返回值      | Lua 返回值                 |
| ----------------- | -------------------------- |
| Int               | Int                        |
| String            | String                     |
| multi-line String | Table                      |
| 状态回复          | Table (只有一个 "ok" Key ) |
| 错误回复          | Table (只有一个 "err" Key) |

空结果比较特殊, 其对应 Lua 的 false.

## 2.2 返回值
在很多情况下都需要脚本可以返回值, 比如前面的访问频率限制脚本会返回访问频率是否超限. 在脚本中可以使用 __return__ 语句将值返回给客户端, 如果没有执行 __return__ 语句则默认返回 __nil__.

在返回时, 它也会遵照 2.1 中表格的转换规则.

## 2.3 调用 Lua 脚本
### 2.3.1 EVAL 命令
笔记开头, 在 redis-cli 启动时通过 __--eval__ 参数调用了一次脚本.

```
redis-cli --eval /path/ratelimiting.lua rateKey , 10 3
```

其实, 在 Redis 中提供了 __EVAL__ 命令可以使开发者像调用其他 Redis 内置命令一样调用脚本, 格式如下:

```
EVAL script numkeys key [key ...] arg [arg ...]
```

- script 参数是要执行的 Lua 脚本
    + 可以是一个 Lua 字符串, 如:
        *  "return redis.call('set', KEYS[1], 'bar')"
    + 可以是一个 Lua 脚本文件
        * 脚本内容不应该是个 Fun, 而只是一段代码块, 注意要有返回值
- numkeys 是脚本要处理的数据库键的数量
    + 不需要任何参数时也不能省略这个参数, 设为 O
- key [key …] 参数指定了脚本要处理的数据库键
    + 在脚本里面通过访问 KEYS 数组来取得, 比如 KEYS[1] 就取出第一个输入的键
- arg [arg …] 参数指定了脚本要用到的 value
    + 在脚本里面可以通过访问 ARGV 数组来获取这些参数

之所以这么设置, 是为了以后操作集群, 暂时先强记着, 后面用到集群时再单独分析.

### 2.3.1 EVALSHA 命令
任何 Lua 脚本, 只要被 EVAL 命令执行过一次, 就会被储存到服务器的脚本缓存里面, 用户只要通过
EVALSHA 命令, 指定被缓存脚本的 __SHA1__ 值, 就可以在不发送脚本的情况下, 再次执行脚本:

假设已知一个 SHA1 值:

```
EVALSHA sha1 numkeys key [key ...] arg [arg ...]
```

那怎么获取 SHA 值呢? 看 2.3.2

### 2.3.2
- SCRIPT EXISTS sha1 [sha1 ...]
    + 检查 sha1 值所代表的脚本是否已经被加入到脚本缓存里面, 是的话返回 1 , 不是的话返回 0
- SCRIPT LOAD script
    + 将脚本储存到脚本缓存里面, 等待将来 EVALSHA 使用
- SCRIPT FLUSH
    + 清除脚本缓存储存的所有脚本
- SCRIPT KILL
    + 杀死运行超时的脚本
        * 如果脚本已经执行过写入操作, 那么还需要使用 SHUTDOWN NOSAVE 命令来强制服务器不保存数据, 以免错误的数据被保存到数据库里面

__SCRIPT LOAD 结合 EVALSHA__

```
SCRIPT LOAD "return 'hello moto'"
"232fd51614574cf0867b83d384a5e898cfd24e5a"

EVALSHA "232fd51614574cf0867b83d384a5e898cfd24e5a" 0
"hello moto"
```



### Redis 中可用的 Lua 库
Redis 在 Lua 环境里面载入了一些常用的函数库, 我们可以使用这些函数库, 直接在脚本里面处理数据, 它们分别是

标准库:

- base 库: 包含 Lua 的核心（core）函数, 比如 assert、tostring、error、type 等
- string 库: 包含用于处理字符串的函数, 比如 find、format、len、reverse 等
- table 库: 包含用于处理表格的函数, 比如 concat、insert、remove、sort 等
- math 库: 包含常用的数学计算函数, 比如 abs、sqrt、log 等
- debug 库: 包含调试程序所需的函数, 比如 sethook、gethook 等

以及外部库

- struct 库: 在 C 语言的结构和 Lua 语言的值之间进行转换
- cjson 库: 将 Lua 值转换为 JSON 对象, 或者将 JSON 对象转换为 Lua 值
- cmsgpack 库: 将 Lua 值编码为 MessagePack 格式, 或者从 MessagePack 格式里面解码出 Lua
- redis.sha1hex: 这是一个函数, 不是库, 用来计算 sha 值
    + eval 'return redis.sha1hex(ARGV[1])' 1 "foo"

__Redis 禁止使用 Lua 标准库中与文件或系统调用相关的函数, 另外 Redis 还禁用 Lua 的全局变量.__

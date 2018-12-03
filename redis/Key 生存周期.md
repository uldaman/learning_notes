我们已经知道有下面四种命令可以设置 key 的生存周期.

- __expire__: 设置 key 的过期时间, 单位秒
    + 语法 expire key seconds
    + set/getset 命令, 会清除 key 的过期时间
- __expireat__: 设置 key 的过期时间点(在指定时间戳过期), 单位秒
    + 语法 expireat key timestamp
    + set/getset 命令, 会清除 key 的过期时间
- __pexpire__: 设置 key 的过期时间, 单位毫秒
    + 语法 pexpire key millisecond
    + set/getset 命令, 会清除 key 的过期时间
- __pexpireat__: 设置 key 的过期时间点(在指定时间戳过期), 单位毫秒
    + 语法 pexpireat key timestamp
    + set/getset 命令, 会清除 key 的过期时间

针对 key 的生存周期补充一些知识:

生存时间可以通过使用 __DEL__ 命令来删除整个 key 来移除, 或者被 __SET__ 和 __GETSET__ 命令覆写(overwrite), 这意味着, 如果一个命令只是修改(alter)一个带生存时间的 key 的值而不是用一个新的 key 值来代替(replace)它的话, 那么生存时间不会被改变.

比如说, 对一个 key 执行 __INCR__ 命令, 对一个列表进行 __LPUSH__ 命令, 或者对一个哈希表执行 __HSET__ 命令, 这类操作都不会修改 key 本身的生存时间.

另一方面, 如果使用 __RENAME__ 对一个 key 进行改名, 那么改名后的 key 的生存时间和改名前一样.

使用 __PERSIST__ 命令可以在不删除 key 的情况下, 移除 key 的生存时间, 让 key 重新成为一个『持久的』(persistent) key .

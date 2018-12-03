MongoDB 提供一个 HTTP 协议的 REST 的服务, 这个端口是你 Server 端口加上 1000, 比如你的 Server 端口为 27017, 那么这个HTTP端口就是 28017.

MongoDB 的 REST 服务, 有的版本是默认开启的, 有的没有, 需要在启动 mongodb server 时用 __--rest__ 开启(如果是默认开启的, 也可以使用 __--nohttpinterface__ 来关闭).

MongoDB 自带的 REST, 不支持 增、删、改, 同时也不支持权限认证, MongoDB 也说它自己提供的认证很简单~~<br>
要想使以上这些支持可以考虑用 [sleepy.mongoose](https://github.com/10gen-labs/sleepy.mongoose)

通过浏览器访问相应端口可以打开 MongoDB REST 的管理页面, 上面显示了很多 Server 相关的信息.

![](http://i66.tinypic.com/1zd5buh.jpg)

__支持的操作:__

- 列出 __databaseName__ 数据库中的 __collectionName__ 集合下的所有数据:
    + http://127.0.0.1:28017/databaseName/collectionName/
- 给上面的数据集添加一个 __limit__ 参数限制返回 10 条
    + http://127.0.0.1:28017/databaseName/collectionName/?limit=-10
- 给上面的数据加上一个 __skip__ 参数设定跳过 5 条记录
    + http://127.0.0.1:28017/databaseName/collectionName/?skip=5
- 同时加上 __limit__ 限制和 __skip__ 限制
    + http://127.0.0.1:28017/databaseName/collectionName/?skip=5&limit=10
- 按条件 __{a: 1}__ 进行结果筛选 (在关键字 __filter__ 后面接上你的字段名)
    + http://127.0.0.1:28017/databaseName/collectionName/?filter_a=1
- 加条件的同时再加上 __limit__ 限制返回条数
    + http://127.0.0.1:28017/databaseName/collectionName/?filter_a=1&limit=-10
- 执行任意命令, 可以通过 __admin/$cmd__, 相当于 __runCommand__
    + 执行 {listDatabase: 1} 命令:
        * http://localhost:28017/admin/$cmd/?filter_listDatabases=1&limit=1
    + 查询集合的记录个数:
        * http://localhost:28017/admin/$cmd/?filter_count=collection&limit=1

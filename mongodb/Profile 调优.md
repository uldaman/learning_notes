## 1. 概述
Mongodb Profile 用于记录慢查询, 默认记录在 system.profile 中.

但 system.profile 是固定大小的 collection, 默认大小是 1M, 如果要修改该大小， 用下面的方法:

```js
db.setProfilingLevel(0)
db.system.profile.drop()
db.createCollection( "system.profile", { capped: true, size:4096000 } )
db.setProfilingLevel(1)
```

## 2. 查看当前 Profile
查看当前是否开启 Profile 功能用命令:

```js
db.getProfilingLevel()
```

返回 Level 等级, 返回值:

- 0 关闭
- 1 记录慢命令, 默认为大于 100 ms
- 2 记录全部

## 3.开启profiling功能
```js
db.setProfilingLevel(1,1000)
{ "was" : 0, "slowms" : 20, "ok" : 1 }
```

第一个参数表示 Profile 的级别, 第二个参数是定义慢查询的阈值

## 4. system.profile
通过 db.system.profile.find() 查看当前的监控日志.

```js
db.system.profile.find()
{ "ts" : ISODate("2014-05-05T02:45:10.359Z"), "info" : "query test.system.profile reslen:36 nscanned:0  \nquery: {}  nreturned:0 bytes:20", "millis" : 0 }
{ "ts" : ISODate("2014-05-05T02:45:13.562Z"), "info" : "query test.$cmd ntoreturn:1 command: { count: \"system.profile\", query: {}, fields: {} } reslen:64 bytes:48", "millis" : 0 }
{ "ts" : ISODate("2014-05-05T02:45:13.562Z"), "info" : "query test.system.profile ntoreturn:5 reslen:36 nscanned:2  \nquery: { query: { millis: { $gt: 0.0 } }, orderby: { $natural: -1.0 } }  nreturned:0 bytes:20", "millis" : 0 }
{ "ts" : ISODate("2014-05-05T02:45:17.171Z"), "info" : "query test.emp reslen:261 nscanned:5  \nquery: {}  nreturned:5 bytes:245", "millis" : 0 }
```

返回值说明:

- ts: 该命令执行的时间
- info: 本命令的详细信息
- reslen: 返回结果集的大小
- nscanned: 本次查询扫描的记录数
- nretured: 本次查询时间返回的结果集
- millis: 该命令执行的耗时

然后针对结果, 如果发现时间比较长, 那么就需要作优化.

比如 __nscanned__ 数很大, 或者接近记录总数, 那么可能没有用到索引查询.

__reslen__ 很大, 有可能返回没必要的字段.

__nreturned__ 很大，那么有可能查询的时候没有加限制.

# 1. 慎用 local 数据库
local 数据库, 从名字可以看出, 它只会在本地存储数据, 即 local 数据库里的内容不会同步到副本集里其他节点上去; 目前 local 数据库主要存储副本集的配置信息、oplog 信息, 这些信息是每个 Mongod 进程独有的, 不需要同步到副本集种其他节点.

在使用 MongoDB 时, 重要的数据千万不要存储在 local 数据库中, 否则当一个节点故障时, 存储在 local 里的数据就会丢失.

另外, 对于重要的数据, 除了不能存储在 local 数据库, 还要注意 MongoDB 默认的 WriteConcern 是 __{w: 1}__, 即数据写到 Primary 上(不保证 journal 已经写成功)就向客户端确认, 这时同样存在丢数据的风险.<br>
对于重要的数据, 可以设置更高级别的如 __{w: "majority"}__ 来保证数据写到大多数节点后再向客户端确认, 当然这对写入的性能会造成一定的影响.

# 2. 慎用 admin 数据库
当 Mongod 启用 auth 选项时, 用户需要创建数据库帐号, 访问时根据帐号信息来鉴权, 而数据库帐号信息就存储在 admin 数据库下.

```
use admin
db.getCollectionNames()
[ "system.users", "system.version" ]
```


- system.users 存储了数据库帐号信息
- system.version 存储 authSchema 的版本信息
- 如果用户创建了自定义的角色, 还会有 system.roles 集合

用户可以在 admin 数据库下建立任意集合, 存储任何数据, 但__强烈__建议不要使用 admin 数据库存储应用业务数据, 最好创建新的数据库.

admin 数据库里的 system.users、system.roles 2 个集合的数据, MongoDB 会 cache 在内存里, 这样不用每次鉴权都从磁盘加载用户角色信息.<br>
目前 cache 的维护代码, 只有在保证 system.users、system.roles 的写入都串行化的情况下才能正确工作, 详情参考官方 issue [SERVER-16092](https://jira.mongodb.org/browse/SERVER-16092)

从代码中我们可以看出, MongoDB 将 admin 数据库上的__意向写锁__(MODE\_IX)直接升级为__写锁__(MODE\_X), 也就是说 admin 数据库的写入操作的锁级别只能到 DB 级别, 不支持多个 collection 并发写入, 在写入时也不支持并发读取.<br>
所以, 如果用户在 admin 数据库里存储业务数据, 则可能遭遇性能问题.

```
if (supportsDocLocking() || enableCollectionLocking) {
    if (supportsDocLocking() || enableCollectionLocking) {
    // The check for the admin db is to ensure direct writes to auth collections
    // are serialized (see SERVER-16092).
    if (_id == resourceIdAdminDB &amp;&amp; !isRead) {
    _mode = MODE_X;
}

_lockState-&gt;lock(_id, _mode);
```


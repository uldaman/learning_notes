ORM技术: Object-Relational Mapping, 把关系数据库的表结构映射到对象上, 简单的说, 就是把数据库表的一行记录与一个对象互相做自动转换.

[TOC]

### 1. 特色
- 支持工作单元的概念
- 强大的会话(session)管理机制
- 擅长多数据库处理

### 2. 安装
pip install sqlalchemy

验证:

```python
import sqlalchemy
sqlalchemy.__version__
```

### 3. 应用
以 MySql 为例

- 创建连接
- 创建映射
- 创建会话

#### 3.1 创建连接
需要注意, ORM 是针对表的映射, 所以在使用 ORM 之前, 得__先建好库__

这里我新建了个数据库, 名为 sqldb, 里面是空的, 还没有表, 现在开始初始化与数据库的连接.

```python
from sqlalchemy import create_engine
engine = create_engine('mysql://root:123456@127.0.0.1:3306/sqldb')
```

__create_engine()__ 用来初始化数据库连接, SQLAlchemy 用一个字符串表示连接信息:

```
'数据库类型 : // 用户名 : 口令 @ 机器地址 : 端口号 / 数据库名'
```

create_engine() 还有个 bool 型的可选参数 __echo__,  __echo__ 为 True 时, 表示打印 sql 语句, __echo__ 为 False 时, 表示不打印.

#### 3.2 声明映射
##### 3.2.1 定义类
```python
from sqlalchemy.ext.declarative import declarative_base
Base = declarative_base()

from sqlalchemy import Column, Integer, String
class User(Base):
    __tablename__ = 'users'
    id = Column(Integer, primary_key = True) # primary_key 是否主键
    name = Column(String(10))
    pwd = Column(String(10))
```

__declarative_base()__ 创建数据库映射的__基类__, 后面与数据中的表相映射的对象必须继承这个基类.

__class User__ 声明数据库表的映射,

__Column__ 声明数据库中列的映射

__Integer、String__ 声明数据库表中存放的数据的类型.

##### 3.2.2 创建表
```python
User.metadata.create_all(engine)
```

在数据库中创建刚才定义的 __class User__ 中设计的表.

如果数据库中已存在这个表, 就不需要执行这个命令了.


#### 3.3. 创建会话
```python
from sqlalchemy.orm import sessionmaker
DBSession = sessionmaker(bind=engine)
```

创建映射后, 我们想要与这个数据表进行通信(获取或者存入数据), 就得先与这个表建立一个连接, session 就是这个连接.

#### 3.4. 持久化对象
上一步中, 建立了会话后, 就可以通过这个会话向数据库中写入数据了.

```python
# 创建 session 对象
session = DBSession()
# 创建新 User 对象
eg_user = USer(id = 2, name = 'eg', pwd = 'thispwd')
# 添加到 session
session.add(eg_user)
# 提交即保存到数据库
session.commit()
# 关闭session
session.close()
```

除了使用 __add__ 单个添加外, 还可以使用 __add\_all__ 添加多个.

#### 3.5. 查询
##### 3.5.1 基础查询
同样的, 建立了会话后, 还可以从数据库中取数据.

```python
# 创建 session
session = DBSession()
# 创建 Query 查询，filter 是 where 条件, 最后调用 one() 返回唯一行, 如果调用 all() 则返回所有行
user = session.query(User).filter(User.id == '5').one()
# 打印类型和对象的name属性
print 'type:', type(user)
print 'name:', user.name
# 关闭Session
session.close()
```

执行结果如下:

```
type: <class '__main__.User'>
name: eg
```

可见, ORM 就是把数据库表的行与相应的对象建立关联, 互相转换.

##### 3.5.2. 多条件查询
```python
from sqlalchemy import and_, or_

session = DBSession()
# and 条件查询
user_and = session.query(User).filter(and_(User.id == '5', User.pwd == '123')).one()
# or 条件查询
user_or = session.query(User).filter(or_(User.id == '5', User.pwd == '123')).one()
session.close()
```

##### 3.5.3. 其他常用查询
```
# 简单查询
session.query(User).all()
session.query(User.name, User.fullname).all()
session.query(User, User.name).all()

# 带条件查询
session.query(User).filter_by(name='user1').all()
session.query(User).filter(User.name == "user").all()
session.query(User).filter(User.name.like("user%")).all()

# 多条件查询
session.query(User).filter(and_(User.name.like("user%"), User.fullname.like("first%"))).all()
session.query(User).filter(or_(User.name.like("user%"), User.password != None)).all()

# sql过滤
session.query(User).filter("id>:id").params(id=1).all()

# 关联查询
session.query(User, Address).filter(User.id == Address.user_id).all()
session.query(User).join(User.addresses).all()
session.query(User).outerjoin(User.addresses).all()

# 聚合查询
session.query(User.name, func.count('*').label("user_count")).group_by(User.name).all()
session.query(User.name, func.sum(User.id).label("user_id_sum")).group_by(User.name).all()

# 子查询
stmt = session.query(Address.user_id, func.count('*').label("address_count")).group_by(Address.user_id).subquery()
session.query(User, stmt.c.address_count).outerjoin((stmt, User.id == stmt.c.user_id)).order_by(User.id).all()

# exists
session.query(User).filter(exists().where(Address.user_id == User.id))
session.query(User).filter(User.addresses.any())
```

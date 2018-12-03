之前笔记介绍了一下连表.

连表(多对多, 一对多)插入数据的方式一样, 都是通过 **对象1.关联数据.add(对象2)** 的方式来添加; 还可以通过 **对象2.类1(小写)_set.add(对象1)** 来添加.

如, 一个 多对多 的关系模型:

```python
class User(models.Model): # 表名
    account = models.CharField(max_length=50) # 字段

class Group(models.Model): # 表名
    groupName = models.CharField(max_length=50) # 字段
    user_relation = models.ManyToManyField("User") # 建立关系, Django 会自动建立中间表
```

如果要插入关联, 先要获取一个 Group 对象 g1, 再获取一个 User 对象 u1, 然后使用, **g1.user_relation.add(u1)** 的方式就能插入数据. 或者** u1.group_set.add(g1).**

**一对多**

```python
class UserType(models.Model): # 表名
    name = models.CharField(max_length=50) # 字段

# Create your models here.
class UserInfo(models.Model): # 表名
    username = models.CharField(max_length=50) # 字段
    password = models.CharField(max_length=50) # 字段
    '''
    建立外键
    字段名称＋ "_id" 做为数据库中的列名称
    这里最终数据库中就是 typeId_id
    '''
    typeId = models.ForeignKey("UserType")
```

UserInfo 中的 typeId 其实是一个对象, 通过它可以获取到另一张表的内容, 语法就是 **__ (两个下划线)**, 如:

`UserInfo.object.filter(typeId__name = "王四") # 连表访问`

**多对多**

还是上面那个 多对多 模型:


```python
class User(models.Model): # 表名
    account = models.CharField(max_length=50) # 字段

class Group(models.Model): # 表名
    groupName = models.CharField(max_length=50) # 字段
    user_relation = models.ManyToManyField("User") # 建立关系, Django 会自动建立中间表
```

# 获取数据
`group_obj.user_relation.all()`

`group_obj.user_relation.all().filter(id=1)`

# 获取数据
`user_obj.group_set.all()`

`user_obj.group_set.all().filter(groupName='CEO')`

`user_obj.group_set.all().filter(groupName='CTO')`

# 场景一
```python
class Data_test(object):
    day = 0
    month = 0
    year = 0
    def __init__(self, year=0, month=0, day=0):
        self.day=day
        self.month=month
        self.year=year

    @classmethod
    def get_date(cls, data_as_string):
        # 这里第一个参数是 cls,  表示调用当前的类名
        year, month, day = map(int, string_date.split('-'))
        date = cls(year, month, day)
        # 返回的是一个初始化后的类
        return date

    def out_date(self):
        print "year :"
        print self.year
        print "month :"
        print self.month
        print "day :"
        print self.day
```

在 Date_test 类里面创建一个成员函数, 前面用了 `@classmethod` 装饰. 它的作用就是有点像静态类, 比静态类不一样的就是它可以传进来一个当前类作为第一个参数 (注意是当前类, 不是当前实例), 另外, 这个参数也是多态的, 即如果是子类调用, 那么这个参数就是子类.

调用:

```python
r = Data_test.get_date('2016-8-6')
r.out_date()
```

输出:

```
year :
2016
month :
8
day :
1
```

这样子等于先调用 `get_date()` 对字符串进行出来, 然后才使用 `Data_test` 的构造函数初始化.

这样的好处就是你以后重构类的时候不必要修改构造函数, 只需要额外添加你要处理的函数, 然后使用装饰符 `@classmethod` 就可以了.

# 场景二
例如一个 User 类对应数据库中的 user 表. 并且有一个 where 方法, 其作用相当于 sql 的 `select * from table where ...`, 此时, 这个 where 方法就可以作为 `@classmethod`:

```python
class User():
    @classmethod
    def where(cls):
        pass
```

这样, 就可以直接通过调用 `User.where()` 来取得数据库中的所有用户对象, 而不需要新建一个 `User` 类的对象之后再调用这个方法.

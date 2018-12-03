Django 对数据库的操作比较简单, 它已经对数据库进行了封闭, 我们无需去关注数据库的细节(即无需像三层架构那样对数据库进行封闭, Django 已经为我们封装好了).

首先, 打开工作目录下的 settings.py 文件, 修改 DATABASES:


```python
DATABASES = {
    'default': {
        'ENGINE':'django.db.backends.mysql',
        'NAME':'newdjango',
        'USER':'root',
        'PASSWORD':'admin',
        'HOST':'',
        'PORT':'3306',
    }
}
```

上面的 NAME 指的是数据库名, 如下图:

![](http://i62.tinypic.com/28vwrvc.jpg)

接下来, 检查下 settings.py 里的 INSTALLED_APPS 是否包含 app:

```python
INSTALLED_APPS = (
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'web',
)
```

这里是最后一个 'web'.

Django 不能自动为我们创建数据库, 但是可以为自动创建表, 现在在 models.py 中设置表和字段.

```python
#-*- coding:utf-8 -*-
from django.db import models

# Create your models here.
class UserInfo(models.Model): # 表名
    username = models.CharField(max_length=50) # 指定字段
    password = models.CharField(max_length=50) # 指定字段
```

然后, 依次执行命令:

**python manage.py makemigrations** -- 基于当前的 Model 创建新的策略文件

**python manage.py migrate** -- 让我们在修改 Model 后可以在不影响现有数据的前提下重建表结构

python manage.py syncdb -- migrate 已具有 syncdb 的功能, 所以无需再执行

Python 1.7 以下的版本执行 syncdb.

如果报错: No module named MySQLdb
那么就是没有安装 MySQL Python 模块, 安装下即可, 用 pip 似乎有点问题, 可以下载[http://yunpan.cn/cm69fkFPDirFu](http://yunpan.cn/cm69fkFPDirFu) 访问密码 2f0c 安装即可.

**常用字段**

<table cellpadding="2" width="798" border="0" cellspacing="0" class="table" >
<tbody >
<tr ><td width="200" valign="top" >models.AutoField
</td><td width="596" valign="top" >`自增列
```如果没有的话, 默认会生成一个名称为 ``id` `的列, 如果要显示的自定义一个自增列, 必须将给列设置为主键 primary_key``=``True.``
</td></tr>
<tr ><td width="200" valign="top" >models.CharField
</td><td width="596" valign="top" >字符串字段
必须指定 max_length 参数.
</td></tr>
<tr ><td width="200" valign="top" >models.BooleanField
</td><td width="596" valign="top" >`布尔类型``
`不能为空, Blank``=``True.``
</td></tr>
<tr ><td width="200" valign="top" >models.ComaSeparatedIntegerField
</td><td width="596" valign="top" >用逗号分割的数字
继承 CharField, 所以必须指定 max_lenght 参数.
</td></tr>
<tr ><td width="200" valign="top" >models.DateField
</td><td width="596" valign="top" >日期类型 date
`对于参数, auto_now ``=` `True` `则每次更新都会更新这个时间; auto_now_add 则只是第一次创建添加, 之后的更新不再改变.`
</td></tr>
<tr ><td width="200" valign="top" >models.DateTimeField
</td><td width="596" valign="top" >日期类型 datetime
同DateField的参数.
</td></tr>
<tr ><td width="200" valign="top" >models.Decimal
</td><td width="596" valign="top" >十进制小数类型
必须指定整数位 max_digits 和小数位 decimal_places.
</td></tr>
<tr ><td width="200" valign="top" >models.EmailField
</td><td width="596" valign="top" >字符串类型
对字符串进行正则表达式.
</td></tr>
<tr ><td width="200" valign="top" >models.FloatField
</td><td width="596" valign="top" >`浮点类型 ``double`
</td></tr>
<tr ><td width="200" valign="top" >models.IntegerField
</td><td width="596" valign="top" >整形
</td></tr>
<tr ><td width="200" valign="top" >models.BigIntegerField
</td><td width="596" valign="top" >长整形
</td></tr>
<tr ><td width="200" valign="top" >models.SmallIntegerField
</td><td width="596" valign="top" >数字
`数据库中的字段有: tinyint、smallint、``int``、bigint`
</td></tr>
<tr ><td width="200" valign="top" >models.PositiveIntegerFiel
</td><td width="596" valign="top" >正 Integer
</td></tr>
<tr ><td width="200" valign="top" >models.PositiveSmallIntegerField
</td><td width="596" valign="top" >正 SmallInteger

`integer_field_ranges ``=` `{`

```'IntegerField'``: (``-``2147483648``, ``2147483647``),`

```'BigIntegerField'``: (``-``9223372036854775808``, ``9223372036854775807``),`

```'PositiveSmallIntegerField'``: (``0``, ``32767``),`

```'PositiveIntegerField'``: (``0``, ``2147483647``),`

`'SmallIntegerField'``: (``-``32768``, ``32767``),`

`}`

</td></tr>
<tr ><td width="200" valign="top" >models.IPAddressField
</td><td width="596" valign="top" >字符串类型（ip4正则表达式）
</td></tr>
<tr ><td width="200" valign="top" >models.GenericIPAddressField
</td><td width="596" valign="top" >字符串类型（ip4和ip6是可选的）

`参数 protocol 可以是: both、ipv4、ipv6`

`验证时, 会根据设置报错`

</td></tr>
<tr ><td width="200" valign="top" >models.NullBooleanField
</td><td width="596" valign="top" >允许为空的布尔类型
</td></tr>
<tr >
<td width="200" valign="top" >models.SlugField
</td>
<td width="596" valign="top" >减号、下划线、字母、数字
</td></tr>
<tr >
<td width="200" valign="top" >models.TextField
</td>
<td width="596" valign="top" >`字符串`` ``longtext`
</td></tr>
<tr >
<td width="200" valign="top" >models.TimeField
</td>
<td width="596" valign="top" >时间 HH:MM[:ss[.uuuuuu]]
</td></tr>
<tr >
<td width="200" valign="top" >models.URLField
</td>
<td width="596" valign="top" >字符串, 地址正则表达式
</td></tr>
<tr >
<td width="200" valign="top" >models.BinaryField
</td>
<td width="596" valign="top" >`二进制<br>``23``、models.ImageField
图片<br>``24``、models.FilePathField 文件`
</td></tr></tbody></table>

**常用参数**

<table cellpadding="2" width="800" border="0" cellspacing="0" class="table" >
<tbody >
<tr >
<td width="200" valign="top" >null = True
</td>
<td width="598" valign="top" >数据库中字段是否可以为空
</td></tr>
<tr >
<td width="200" valign="top" >blank = True
</td>
<td width="598" valign="top" >django 的 Admin 中添加数据时是否可允许空值
</td></tr>
<tr >
<td width="200" valign="top" >primary_key = False
</td>
<td width="598" valign="top" >主键，对AutoField设置主键后，就会代替原来的自增 id 列
</td></tr>
<tr >
<td width="200" valign="top" >auto_now
</td>
<td width="598" valign="top" >自动创建---无论添加或修改，都是当前操作的时间
</td></tr>
<tr >
<td width="200" valign="top" >auto_now_add
</td>
<td width="598" valign="top" >自动创建---永远是创建时的时间
</td></tr>
<tr >
<td width="200" valign="top" >choices
</td>
<td width="598" valign="top" >

GENDER_CHOICE = (
(u'M', u'Male'),
(u'F', u'Female'),
)
gender = models.CharField(max_length=2,choices = GENDER_CHOICE)

</td></tr>
<tr >
<td width="200" valign="top" >max_length
</td>
<td width="598" valign="top" >
</td></tr>
<tr >
<td width="200" valign="top" >default
</td>
<td width="598" valign="top" >默认值
</td></tr>
<tr >
<td width="200" valign="top" >verbose_name
</td>
<td width="598" valign="top" >Admin 中字段的显示名称
</td></tr>
<tr >
<td width="200" valign="top" >name|db_column
</td>
<td width="598" valign="top" >数据库中的字段名称
</td></tr>
<tr >
<td width="200" valign="top" >unique = True
</td>
<td width="598" valign="top" >不允许重复
</td></tr>
<tr >
<td width="200" valign="top" >db_index = True
</td>
<td width="598" valign="top" >数据库索引
</td></tr>
<tr >
<td width="200" valign="top" >editable = True
</td>
<td width="598" valign="top" >在 Admin 里是否可编辑
</td></tr>
<tr >
<td width="200" valign="top" >error_messages = None
</td>
<td width="598" valign="top" >错误提示
</td></tr>
<tr >
<td width="200" valign="top" >auto_created = False
</td>
<td width="598" valign="top" >自动创建
</td></tr>
<tr >
<td width="200" valign="top" >help_text
</td>
<td width="598" valign="top" >在 Admin 中提示帮助信息
</td></tr>
<tr >
<td width="200" valign="top" >validators = []
</td>
<td width="598" valign="top" >
</td></tr>
<tr >
<td width="200" valign="top" >upload-to
</td>
<td width="598" valign="top" >
</td></tr></tbody></table>

**外键 -- 多对一**

用来形成表表对应, 即让一张表里的某些字段和另一张表里的 ID 字段形成联系.

```python
#-*- coding:utf-8 -*-
from django.db import models

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


**外键 -- 多对多**

要实现多对多, 需要再创建一个"中间表", 使用 Django, 它会自动帮我们创建.

```python
class User(models.Model): # 表名
    account = models.CharField(max_length=50) # 字段

class Group(models.Model): # 表名
    groupName = models.CharField(max_length=50) # 字段
    user_relation = models.ManyToManyField("User") # 建立关系, Django 会自动建立中间表
```

![](http://i61.tinypic.com/fbe4pw.jpg)

因为我也是从 C++ 转向 Python 的, 所以总感觉自己在用写 C++ 代码的思维写 Python, 没有真正用到其作为脚本语言的优势, 很像披着 Python 外衣的 C++ 代码.

所以才有了这篇 wiki, 相当于对之前学过的知识做个小结, 并且收集一些 Pythonic 的代码.

> 注意, 部分内容收集自互联网;

> 另外推荐本书: [<<编写高质量代码 - 改善 Python 程序的 91 个建议>>](https://yunpan.cn/c6mPavfeDzHYK)  **访问密码** 9f75

# References:

[python 性能调优](https://github.com/uldaman/CS_Offer/blob/master/Python/Optimization.md)

[让你的Python代码更加pythonic](http://wuzhiwei.net/be_pythonic/)

[关于 Python 的最全面试题](https://github.com/uldaman/interview_python)

# 列表生成式
提供一个可迭代的对象生成一个新列表

`[exp for iter_var in iterable]`

`[exp for iter_var in iterable if cond_exp]`

首先迭代 iterable 里所有内容, 每一次迭代, 都把 iterable 里相应内容放到 iter\_var 中, 再在表达式 exp 中应用该 iter\_var 的内容, 最后用表达式的计算值生成一个新的列表.

例如, 把一个 list 中所有的字符串变成小写:

```python
L = ['Hello', 'World', 'IBM', 'Apple']
[s.lower() for s in L]
>>> ['hello', 'world', 'ibm', 'apple']
```

参考链接: [Python 高级特性之列表生成式](http://www.smallcpp.cn/python-gao-ji-te-xing-zhi-lie-biao-sheng-cheng-shi.html)

# 生成器表达式
当列表过长, 而我们也不需要一次性获取全部数据时, 应当考虑使用生成器(generator)表达式而不是列表解析;

生成器表达式的语法和列表解析一样, 只不过生成器表达式是被（）括起来的, 而不是 [ ], 如下:

`(exp for iter_var in iterable)`

`(exp for iter_var in iterable if cond_exp)`

生成器表达式使用了 "惰性计算" (lazy evaluation, 也有翻译为 "延迟求值", 我以为这种按需调用 call by need 的方式翻译为惰性更好一些), 只有在检索时才被赋值 (evaluated), 所以在列表比较长的情况下使用更有效 (节省内存).

参考链接: [Python 高级特性之生成器表达式](http://www.smallcpp.cn/python-gao-ji-te-xing-zhi-sheng-cheng-qi-biao-da-shi.html)

# sorted( )
sorted 语法如下:

`sorted(iterable, cmp = None, key = None, reverse = False)`

- iterable: 是可迭代类型
- cmp: 定制一个接收两个参数比较函数, 比较什么由下一个参数 key 决定
- key: 指定一个接收一个参数的函数, 这个函数用于从序列的每个元素中提取一个用于排序的依据
- reverse: 排序规则. reverse = True 或者 reverse = False
- 返回值: 是一个经过排序的可迭代类型, 与 iterable 一样

> 一般来说, cmp 和 key 可以使用 lambda 表达式

正序:

```python
sorted([36, 5, 12, 9, 21])
>>>[5, 9, 12, 21, 36]
```

倒序:

```python
sorted([36, 5, 12, 9, 21], reverse = True)
>>>[36, 21, 12, 9, 5]
```

如果想自己实现倒序排序, 则可以自己写 **cmp** 函数, 然后做为 sorted() 的第二个参数.

通常规定, 对于两个元素 x 和 y:

- 如果认为 x < y, 则返回 -1
- 如果认为 x == y, 则返回 0
- 如果认为 x > y, 则返回 1

我们来实现倒序:

```python
def reversed_cmp(x, y):
    if x > y:
        return -1
    if x < y:
        return 1
    return 0
```

传入自定义的比较函数 reversed_cmp, 就可以实现倒序排序:

```python
sorted([36, 5, 12, 9, 21], reversed_cmp)
>>>[36, 21, 12, 9, 5]
```

再来看年 sorted() 的 **key** 参数:

```python
L = [('b',2),('a',1),('c',3),('d',4)]
print sorted(L, key = lambda x : x[1])
>>>[('a', 1), ('b', 2), ('c', 3), ('d', 4)]
```

L 中的元素是一个 tupe, key 参数指定用 `tupe[1]` 做为依据来比较

参考链接:

[Python 高级特性之 sorted( )](http://www.smallcpp.cn/python-gao-ji-te-xing-zhi-sorted.html)

[Python中sorted()方法的用法](http://www.cnblogs.com/woshitianma/p/3222989.html)

# map( )
map() 函数接收两个参数, 一个是函数, 一个是序列, map() 将传入的函数依次作用到序列的每个元素, 并把结果作为新的 list 返回.

例子, 求 1 到 10 每个数的乘阶:

```python
def fun(x):
    return x * x

map(fun, range(1, 11))
>>>[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

参考链接: [Python 高级特性之 map( )](http://www.smallcpp.cn/python-gao-ji-te-xing-zhi-map.html)

# reduce( )
reduce() 函数接收两个参数, 一个是函数, 一个是序列;

但传入的这个函数必须接收两个参数, reduce() 会把这个函数运算的结果继续和序列的下一个元素做**累积**运算, 相当于:

```python
reduce(fun, [x1, x2, x3, x4]) = fun(fun(fun(x1, x2), x3), x4)
```

例子, 对序列求和:

```python
def add(x, y):
    return x + y

reduce(add, [1, 3, 5, 7, 9])
>>>25
```

参考链接: [Python 高级特性之 reduce( )](http://www.smallcpp.cn/python-gao-ji-te-xing-zhi-reduce.html)

# filter( )
filter() 函数接收两个参数, 一个是函数, 一个是序列;

filter() 把传入的函数依次作用于每个元素, 然后根据返回值是 True 还是 False 决定保留还是丢弃该元素.

例如, 在一个 list 中, 删掉偶数, 只保留奇数, 可以这么写:

```python
def is_odd(n):
    return n % 2 == 1

filter(is_odd, range(1, 11))
>>>[1, 3, 5, 7, 9]
```

参考链接: [Python 高级特性之 filter( )](http://www.smallcpp.cn/python-gao-ji-te-xing-zhi-filter.html)

# lambda 表达式
当我们在传入函数时, 有些时候, 不需要显式地定义函数, 直接传入匿名函数更方便, 匿名函数也叫 lambda 表达式.

lambda 因为是一个表达式, 所以不要写 return, 返回值就是该表达式的结果.

lambda 语法中, 冒号前是参数, 多个用逗号隔开, 冒号右边的返回值.

`lambda x: x * x` 实际上就是:

```python
def f(x):
    return x * x
```

用 lambda 表达式还有个好处, 因为函数没有名字, 不必担心函数名冲突, 此外, lambda 表达式也是一个函数对象, 也可以把 lambda 表达式赋值给一个变量, 再利用变量来调用该函数.

```python
g = lambda x : x ** 2 # x 的平方
v = lambda x, y : x ** y # x 的 y 次方
```

例子, 求 1 到 100 的合:

```python
reduce(lambda x, y : x + y, range(1, 101))
>>>5050
```

lambda 可以配合 map、reduce、filter、sorted 高效操作列表.

参考链接: [Python 高级特性之 lambda](http://www.smallcpp.cn/python-gao-ji-te-xing-zhi-lambda.html)

# 切片
对于具有序列结构的数据来说, 切片操作的语法是:

`consequence[start_index : end_index : step]`

`注意`: 对于序列结构数据来说, **xxx\_index**和**step**都具有正负两个值, 分别表示左右两个方向取值.

- start\_index: 表示切片的起始位置 (**包含**该位置的元素)
    + 默认为序列第一个元素, 用正索引表示为 0
    + 默认为序列第一个元素, 用负索引表示为 `-len(consequence)`
- end\_index: 表示切片的结束位置 (**不包含**该位置的元素)
    + 默认为序列最后一个元素, 用正索引表示为 `len(consequence) - 1`
    + 默认为序列最后一个元素, 用负索引表示为 \-1
- step: 表示取值的步长, 默认为 1, 步长值不能为 0
    + 正数表示向正向递增取值
    + 负数表示向反向递增取值

**xxx\_index**和**step**并不是必须的, 可以有下面这些组合:

- `con[start_index]`: 返回索引值为 start\_index 的对象, start\_index 为 -len(con) 到 len(con) \- 1 之间任意整数
- `con[start_index : end_index]`: 返回索引值为 start\_index 到 end\_index \- 1 之间的连续对象
- `con[start_index : end_index : step]`: 从 start\_index 开始, 每递增 step 位取个值, 到 end\_index \- 1 结束
- `con[start_index:]`: 缺省 end\_index, 表示从 start\_index 开始到序列中最后一个对象
- `con[:end_index］`: 缺省 start\_index, 表示从序列中第一个对象到 end\_index \- 1 之间的片段
- `con[:]`: 缺省 start\_index 和 end\_index, 表示从第一个对象到最后一个对象的完整片段
- `con[::step]`: 缺省 start\_index 和 end\_index, 表示对序列按 step 正向/反向取值

例子, 返转字符串

```python
def reverse_str( s ):
    return s[::-1]
```

字符串也是一种序列, 因此利用切片就能极方便地反转字符了, 步长为 \-1 即反向、每次递增 1 地从序列中取值.

# 用正确的数据结构
**字典 (dictionary) 与列表 (list)**:

Python 字典中使用了 hash table, 因此查找操作的复杂度为 O(1), 而 list 实际是个数组, 在 list 中, 查找需要遍历整个 list, 其复杂度为 O(n), 因此对成员的查找访问等操作字典要比 list 更快.

**集合 (set) 与列表 (list)**:

set 的 union、ntersection、difference 操作要比 list 的迭代要快, 因此如果涉及到求 list 交集, 并集或者差的问题可以转换为 set 来操作.

- set(list1) | set(list2)：包含 list1 和 list2 所有数据的新集合
- set(list1) & set(list2)：包含 list1 和 list2 中共同元素的新集合
- set(list1) - set(list2)：在 list1 中出现但不在 list2 中出现的元素的集合

#字符串的优化
python 中的字符串对象是不可改变的，因此对任何字符串的操作如拼接，修改等都将产生一个新的字符串对象，而不是基于原字符串，因此这种持续的 copy 会在一定程度上影响 python 的性能.

`使用 join 而不是 + 连接字符串`

避免类似下面的代码片段:

```python
s = ''
for x in somelist:
    s += x
```

而是要使用下面这种:

```python
s = ''.join(somelist)
```

当对字符串可以使用**正则表达式**或者内置函数来处理的时候，选择内置函数, 如 str.isalpha()、str.isdigit()、str.startswith(('x', 'yz'))、str.endswith(('x', 'yz')) 等.

对字符进行格式化比直接串联读取要快，因此要使用:

`out = '<html>%s%s%s%s</html>'' % (head, prologue, query, tail)`

而不是:

`out = '<html>'' + head + prologue + query + tail + '</html>'`

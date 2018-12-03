Collections 是 Python 内置的一个集合模块, 在 Python 内置数据类型的基础上, 又提供了几个额外的数据类型供我们使用.

- `namedtuple`: 生成可以使用名字来访问元素内容的 tuple 子类
- `deque`: 双端队列, 可以快速的从另外一侧追加和推出对象
- `Counter`: 计数器, 主要用来计数
- `OrderedDict`: 有序字典
- `defaultdict`: 带有默认值的字典

# namedtuple
如果看到一个 tuple `(1, 2)`, 很难看出这是用来表示一个坐标的, 这时, `namedtuple` 就派上了用场：

```python
from collections import namedtuple
Point = namedtuple('Point', ['x', 'y'])
p = Point(1, 2)
```

`namedtuple` 是一个函数, 它用来创建一个自定义的 tuple 对象, 并且规定了 tuple 元素的个数, 并可以用**名称**而不是索引来引用 tuple 的某个元素.

这样一来, 我们用 `namedtuple` 可以很方便地定义一种数据类型, 它具备 tuple 的不变性, 又可以根据属性来引用, 使用十分方便.

可以验证创建的 Point 对象是 tuple 的一种子类：

```
>>> isinstance(p, Point)
True
>>> isinstance(p, tuple)
True
```


# deque
使用 `list` 存储数据时, 按索引访问元素很快, 但是插入和删除元素就很慢了, 因为 list 是线性存储, 数据量大的时候, 插入和删除效率很低; 而 `deque` 是为了高效实现插入和删除操作的双向列表.

deque 其实是 double-ended queue 的缩写, 翻译过来就是双端队列, 它最大的好处就是实现了从队列头部快速增加和取出对象: `popleft()`、`appendleft()`.

关于 `deque` 和 `list` 的差别参考 wiki: [vector、deque、list 三者的区别](http://wiki.smallcpp.cn/%E8%BD%AF%E4%BB%B6%E5%B7%A5%E7%A8%8B/vector%E3%80%81deque%E3%80%81list%20%E4%B8%89%E8%80%85%E7%9A%84%E5%8C%BA%E5%88%AB.html)

# Counter
`Counter` 是一个简单的计数器, 例如, 统计字符出现的个数：

```python
from collections import Counter
c = Counter()
for ch in 'programming':
    c[ch] = c[ch] + 1
```

结果:

```
>>> c
Counter({'g': 2, 'm': 2, 'r': 2, 'a': 1, 'i': 1, 'o': 1, 'n': 1, 'p': 1})
```

`Counter` 实际上也是 `dict` 的一个子类, 上面的结果可以看出, 字符 'g'、'm'、'r' 各出现了两次, 其他字符各出现了一次.

# OrderedDict
在 Python 中, `dict` 这个数据结构由于 hash 的特性, 是无序的, 这在有的时候会给我们带来一些麻烦,  幸运的是, collections 模块为我们提供了 `OrderedDict`, 当你要获得一个有序的字典对象时, 用它就对了.

```python
from collections import OrderedDict
d = dict([('a', 1), ('b', 2), ('c', 3)])
```

结果:

```
d # dict 的Key是无序的
{'a': 1, 'c': 3, 'b': 2}
```

改用 OrderedDict:

```python
od = OrderedDict([('a', 1), ('b', 2), ('c', 3)])
```

结果:

```
od # OrderedDict的Key是有序的
OrderedDict([('a', 1), ('b', 2), ('c', 3)])
```

注意, OrderedDict 的 Key 会按照**插入的顺序**排列, 不是Key本身排序.

# defaultdict
使用 `dict` 时, 如果引用的 Key 不存在, 就会抛出 KeyError. 如果希望 key 不存在时, 返回一个默认值, 就可以用 `defaultdict`:

```python
from collections import defaultdict
dd = defaultdict(lambda: 'N/A')
dd['key1'] = 'abc'
```

结果:

```
dd['key1'] # key1 存在
'abc'

dd['key2'] # key2不存在, 返回默认值
'N/A'
```

注意默认值是调用函数返回的, 而函数在创建 `defaultdict` 对象时传入.

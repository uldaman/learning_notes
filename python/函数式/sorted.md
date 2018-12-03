sorted() 函数接收两个参数, 一个是序列, 一个是函数 (参数顺序和 map/reduce/filter 相反).

python 内置的 sorted() 函数就可以对 list 进行排序:
``` python
sorted([36, 5, 12, 9, 21])
>>>[5, 9, 12, 21, 36]
```

如果想实现倒序排序, 则可以自己写比较函数, 然后做为 sorted() 的第二个参数.

通常规定, 对于两个元素 x 和y:

- 如果认为 x < y, 则返回 -1
- 如果认为 x == y, 则返回 0
- 如果认为 x > y, 则返回 1

```python
def reversed_cmp(x, y):
    if x > y:
        return -1
    if x < y:
        return 1
    return 0
```

传入自定义的比较函数 reversed_cmp，就可以实现倒序排序：
```python
 sorted([36, 5, 12, 9, 21], reversed_cmp)
>>>[36, 21, 12, 9, 5]
```

我们再看一个字符串排序的例子：
```python
sorted(['bob', 'about', 'Zoo', 'Credit'])
>>>['Credit', 'Zoo', 'about', 'bob']
```

默认情况下, 对字符串排序, 是按照 ASCII 的大小比较的, 也就是说, 它是区分大小写的, 由于'Z' < 'a', 所以大写字母 Z 会排在小写字母 a 的前面.

现在, 我们提出排序应该忽略大小写, 按照字母序排序, 要实现这个算法, 只要我们定义出忽略大小写的比较函数就可以:
```python
def cmp_ignore_case(s1, s2):
    u1 = s1.upper()
    u2 = s2.upper()
    if u1 < u2:
        return -1
    if u1 > u2:
        return 1
    return 0
```

忽略大小写来比较两个字符串, 实际上就是先把字符串都变成大写(或者都变成小写)再比较.

这样, 我们给 sorted() 传入上述比较函数, 即可实现忽略大小写的排序:
```python
sorted(['bob', 'about', 'Zoo', 'Credit'], cmp_ignore_case)
>>>['about', 'bob', 'Credit', 'Zoo']
```

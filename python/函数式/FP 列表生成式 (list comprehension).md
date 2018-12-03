已知一个列表 [1, 2, 3, 4, 5, 6, 7, 8, 9, 10], 我们想得到 [1 * 1, 2 * 2, 3 * 3, ..., 10 * 10], 此时, 借助列表生成式可以很方便的实现:

```python
[x * x for x in range(1, 11)]
```

**[x * x for x in range(1, 11)]** 就是一个列表生成式, 它的基础语法是:

**[exp for iter_var in iterable] **

首先迭代 **iterable** 里所有内容, 每一次迭代, 都把 **iterable** 里相应内容放到 **iter\_var** 中, 再在表达式 **exp** 中应用该 **iter\_var** 的内容, 最后用表达式的计算值生成一个新的列表.

除了基础语法外, 列表生成式还可以使用两层循环:
```python
L = [(x, y) for x in range(2) for y in range(3)]
>>[(0, 0), (0, 1), (0, 2), (1, 0), (1, 1), (1, 2)]
```

还可以加上判断语句:
```python
N = [x for x in range(10) if x > 5]
>>[6, 7, 8, 9]
```

***

运用列表生成式, 可以写出非常简洁的代码;

例如, 把一个 list 中所有的字符串变成小写:
```python
L = ['Hello', 'World', 'IBM', 'Apple']
[s.lower() for s in L]
>>> ['hello', 'world', 'ibm', 'apple']
```

***

**注意事项**

**1**.  当需要只是执行一个循环的时候尽量使用循环而不是列表解析, 这样更符合python提倡的直观性:
```python
for item in sequence:
    process(item)
```

**2**.  当有内建的操作或者类型能够以更直接的方式实现的, 不要使用列表解析.
例如复制一个列表时, 使用; **L1=list(L)** 即可, 不必使用: **L1=[x for x in L] **

**3**. 如果需要对每个元素都调用并且返回结果时, 应使用 **L1=map(f,L)**, 而不是 **L1=[f(x) for x in L]**.

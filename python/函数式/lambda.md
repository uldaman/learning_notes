lambda 函数也叫匿名函数, 即函数没有具体的名称;

严格来说, lambda 是一个表达式, 也正因为它是表达式, 所以它可以用在某些不能使用 **def** 的场合, 如:
```python
info = [lambda a: a ** 3, lambda b: b ** 3] # ** 两个乘号表示乘方
```

lambda 语法中, 冒号前是参数, 多个用逗号隔开, 冒号右边的返回值:
```python
g = lambda x : x ** 2 # x 的平方
v = lambda x,y : x ** y # x 的 y 次方
```

看个例子, 求 1 到 100 的合:
```python
reduce(lambda x, y : x + y, range(1, 101))
>>>5050
```

**lambda 可以配合 map、reduce、filter、sorted 高效操作列表**.

reduce() 函数接收两个参数, 一个是函数, 一个是序列;

但传入的这个函数必须接收**两个参数**, reduce() 会把这个函数运算的结果继续和序列的下一个元素做**累积**运算, 相当于:
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

lambda 简化版:
```python
reduce(lambda x, y : x + y, [1, 3, 5, 7, 9])
>>>25
```

再来一个例子, 因为 **python 中字符串 str 也是一个序列**, 所以可以借此实现 python 自带的 int() 函数功能, 把一个字符串转换成整数:
```python
def CharToNum(i):
	return {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}[i]

def StrToInt(s):
	return reduce(lambda x, y : x * 10 + y, map(CharToNum, s))
```

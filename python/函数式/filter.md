filter() 函数接收两个参数, 一个是函数, 一个是序列;

filter() 把传入的函数依次作用于每个元素, 然后根据返回值是 True 还是 False 决定保留还是丢弃该元素.

例如, 在一个 list 中, 删掉偶数, 只保留奇数, 可以这么写:
```python
def is_odd(n):
	return n % 2 == 1

filter(is_odd, range(1, 11))
>>>[1, 3, 5, 7, 9]
```

lambda 简化版:
```python
filter(lambda x : x % 2 == 1, range(1, 11))
```

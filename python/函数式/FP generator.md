# generator
当列表过长, 而我们也不需要一次性获取全部数据时, 应当考虑使用生成器(**generator**)表达式而不是列表解析;

生成器表达式的语法和列表解析一样, 只不过生成器表达式是被（）括起来的, 而不是[ ], 如下:

**(exp for iter_var in iterable)**

**(exp for iter_var in iterable if cond_exp)**
```python
g = (x * x for x in range(10))
>>> g <generator object <genexpr> at 0x1022ef630>
```

如此,  我们得到的就是一个生成器, 如果想要一个一个提取元素出来, 可以通过 **next()** 函数获得 **generator** 的下一个返回值:
```python
next(g)
>>>0
next(g)
>>>1
next(g)
>>>4
.
.
.
next(g)
>>>81
next(g)
Traceback (most recent call last):
	File "<stdin>", line 1, in <module>
StopIteration
```

或者使用 **for** 循环遍历, 因为 **generator** 也是可迭代对象:
```python
g = (x * x for x in range(10))
for n in g:
    print(n)

0
1
4
9
16
25
36
49
64
81
```

生成器表达式并不真正创建数字列表, 而是返回一个生成器, 这个生成器在每次计算出一个条目后, 把这个条目“产生”(**yield**)出来;

生成器表达式使用了“惰性计算”(lazy evaluation, 也有翻译为“延迟求值”, 我以为这种按需调用 call by need 的方式翻译为惰性更好一些), 只有在检索时才被赋值(evaluated), 所以在列表比较长的情况下使用更有效(节省内存).

# next 和 send

对于普通的生成器(参考下面的例子), 第一个 `next` 调用, 相当于启动生成器, 会从生成器函数的第一行代码开始执行, 直到第一次执行完 `yield` 语句 (第4行) 后, 跳出生成器函数.<br>
然后第二个 `next` 调用, 进入生成器函数后, 从 `yield` 语句的下一句语句 (第5行) 开始执行, 然后重新运行到 `yield` 语句, 执行后, 跳出生成器函数，后面再次调用 `next`, 依次类推.

```python
def consumer():
    r = 'here'
    for i in xrange(3):
        yield r
        r = '200 OK' + str(i)

c = consumer()
n1 = c.next()
n2 = c.next()
n3 = c.next()
```

了解了 `next()` 如何让包含 `yield` 的函数执行后, 我们再来看另外一个非常重要的函数 `send(msg)`.

其实 `next()` 和 `send()` 在一定意义上作用是相似的, 区别是 `send()` 可以传递 `yield` 表达式的值进去, 而 `next()` 不能传递特定的值, 只能传递 None 进去.

因此, 我们可以看做 `c.next()` 和 `c.send(None)` 作用是一样的.

需要提醒的是, 第一次调用时, 请使用 `next()` 语句或是 `send(None)`，不能使用 send 发送一个非 None 的值, 否则会出错的 (TypeError), 因为没有 Python yield 语句来接收这个值.

下面来着重说明下 `send` 执行的顺序(参考下面的例子).

当第一次 `send(None)` (对应11行) 时, 启动生成器, 从生成器函数的第一行代码开始执行, 直到第一次执行完 `yield` (对应第四行) 后, 跳出生成器函数, 这个过程中, n 一直没有定义.

下面运行到 `send(1)` 时, 进入生成器函数, 注意这里与调用 `next` 的不同, 这里是从第四行开始执行, 把 1 赋值给 n, 但是并不执行 `yield` 部分.

下面继续从 `yield` 的下一语句继续执行, 然后重新运行到 `yield` 语句, 执行后, 跳出生成器函数.

即 `send` 和 `next` 相比, 只是开始多了一次赋值的动作, 其他运行流程是相同的.

```python
def consumer():
    r = ''
    while True:
        n = yield r
        if not n:
            return
        print('[CONSUMER] Consuming %s...' % n)
        r = '200 OK'

def produce(c):
    c.send(None)
    n = 0
    while n < 5:
        n = n + 1
        print('[PRODUCER] Producing %s...' % n)
        r = c.send(n)
        print('[PRODUCER] Consumer return: %s' % r)
    c.close()

c = consumer()
produce(c)
```

运行结果:

```shell
[PRODUCER] Producing 1...
[CONSUMER] Consuming 1...
[PRODUCER] Consumer return: 200 OK
[PRODUCER] Producing 2...
[CONSUMER] Consuming 2...
[PRODUCER] Consumer return: 200 OK
[PRODUCER] Producing 3...
[CONSUMER] Consuming 3...
[PRODUCER] Consumer return: 200 OK
[PRODUCER] Producing 4...
[CONSUMER] Consuming 4...
[PRODUCER] Consumer return: 200 OK
[PRODUCER] Producing 5...
[CONSUMER] Consuming 5...
[PRODUCER] Consumer return: 200 OK
```

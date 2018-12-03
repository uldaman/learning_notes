python 的 dict 很好用, 可以实现**表驱动**, 有效去除 if\.\.else 和 case 语句.

但是当 value 是函数时, 就需要另外处理, 因为如果直接写函数的话, 会在 dict 初始化时就直接求值, 必须结合 **lambda** 封装函数才能实现函数"表".

比如下面的例子, 使用 lambda 对 doRun 进行封装, 当 dict 初始化时会直接求一次值, 但此时执行的是 lambda 函数, 也就是返回了 doRun 函数.

```python
def doRun():
  print 'run . . .'

fun_dict = {
  'run': lambda: doRun(),
  'add': lambda a, b: a + b,
  'sub': lambda a, b: a - b,
  'mul': lambda a, b: a * b,
  'dev': lambda a, b: a / b
}
```


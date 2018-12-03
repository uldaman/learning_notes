在[Python 高级特性之定制类](http://www.smallcpp.cn/python-gao-ji-te-xing-zhi-ding-zhi-lei.html#9595getattr9595)中已经提到过 `__getattr__` 及其的一种应用 (**Restful API**), 除此之外, `__getattr__` 还可以用来实现组合模式 (需要配合[反射](http://www.smallcpp.cn/51-python-fan-she-zi-sheng.html)) -- 组合优于继承.

```python
class Element(object):
  def foo(self):
    print 'foo in Element'

  def bar(self):
    print 'bar in Element'

class Composite(object):
  def __init__(self):
    self.element = Element()

  def foo(self):
    print 'foo in Composite'
    self.element.foo()

  def __getattr__(self, name):
    return getattr(self.element, name)

if __name__ == '__main__':
  a = Composite()
  a.foo()
  a.bar()
```

如果 `Composite` 需要修改 `Element` 的行为, 那么定义一个同名的属性就行了, 其他的想直接"继承"的属性，通通交给 `__getattr__` 就行了.

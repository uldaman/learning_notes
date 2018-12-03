单元测试, 即对软件设计的最小单元进行内部逻辑、语法、算法、功能等的正解性进行验证.

**References**:<br>
[python unittest](https://docs.python.org/2/library/unittest.html)<br>
[使用 Python Mock 类进行单元测试](http://www.oschina.net/translate/unit-testing-with-the-python-mock-class)

**测试级别:**

- 代码级别
    + 接口测试
    + 数据结构测试
    + 边界测试
- 模块功能级别
    + 黑盒测试
- 其他
    + 性能
    + 代码规范等

**测试过程:**

![](http://i68.tinypic.com/o9loqc.jpg)

# 初始用法 (单个测试用例)
```python
# -*- coding: utf-8 -*-
import unittest

class Add(object):

    def add(self, x, y):
        return x + y

class TestAdd(unittest.TestCase):
    def setUp(self):
        # 构造
        self.obj = Add()

    def runTest(self):
        print self.obj.add(10, 20) == 30

    def test_add(self):
        print self.obj.add(10, 20) == 30

    def tearDown(self):
        # 析构
        self.obj = None

if __name__ == '__main__':
    # demo_add = TestAdd()  # 默认执行 runTest
    demo_add = TestAdd('test_add')  # 执行参数的方法
    demo_add.run()
```

# 中级用法 (测试集)
unittest.TestSuite: 测试用例的集合, 存放测试用例的容器

添加测试集的方法:

- `__init__`
- `addTest(self, test)`
- `addTests(self, tests)`

运行测试集: `TextTestRunner()`

## 简单测试集
```python
# -*- coding: utf-8 -*-
import unittest

class Count(object):

    def add(self, x, y):
        return x + y

    def sub(self, x, y):
        return x - y

class TestCount(unittest.TestCase):
    def setUp(self):
        # 构造
        self.obj = Count()

    def test_add(self):
        print self.obj.add(10, 20) == 30

    def test_sub(self):
        print self.obj.sub(10, 5) == 5

    def tearDown(self):
        # 析构
        self.obj = None

def get_suite():
    demo_count_add = TestCount('test_add')
    demo_count_sub = TestCount('test_sub')
    suite = unittest.TestSuite()
    suite.addTests([demo_count_add, demo_count_sub])
    return suite

if __name__ == '__main__':
    s = get_suite()
    print s.countTestCases()
    runner = unittest.TextTestRunner()
    runner.run(s)
```

## 结合 map
```python
# -*- coding: utf-8 -*-
import unittest

class Count(object):

    def add(self, x, y):
        return x + y

    def sub(self, x, y):
        return x - y

class TestCount(unittest.TestCase):
    def setUp(self):
        # 构造
        self.obj = Count()

    def test_add(self):
        print self.obj.add(10, 20) == 30

    def test_sub(self):
        print self.obj.sub(10, 5) == 10

    def tearDown(self):
        # 析构
        self.obj = None

def get_suite():
    case_list = ['test_add', 'test_sub']
    demos = map(TestCount, case_list)
    suite = unittest.TestSuite()
    suite.addTests(demos)
    return suite

if __name__ == '__main__':
    s = get_suite()
    print s.countTestCases()
    runner = unittest.TextTestRunner()
    runner.run(s)
```

## 使用 \_\_init\_\_
```python
# -*- coding: utf-8 -*-
import unittest

class Count(object):

    def add(self, x, y):
        return x + y

    def sub(self, x, y):
        return x - y

class TestCount(unittest.TestCase):
    def setUp(self):
        # 构造
        self.obj = Count()

    def test_add(self):
        print self.obj.add(10, 20) == 30

    def test_sub(self):
        print self.obj.sub(10, 5) == 10

    def tearDown(self):
        # 析构
        self.obj = None


class CountTestSuite(unittest.TestSuite):

    def __init__(self):
        case_list = ['test_add', 'test_sub']
        demos = map(TestCount, case_list)
        super(CountTestSuite, self).__init__(demos)

if __name__ == '__main__':
    s = CountTestSuite()
    print s.countTestCases()
    runner = unittest.TextTestRunner()
    runner.run(s)
```

## 自动构建测试集
`unitest.makeSuite`

测试方法必须以 规定前缀 开头 (前缀可以通过 makeSuite 的 prefix 参数指定, 默认是 test)

```python
# -*- coding: utf-8 -*-
import unittest

class Count(object):

    def add(self, x, y):
        return x + y

    def sub(self, x, y):
        return x - y


class TestCount(unittest.TestCase):

    def setUp(self):
        # 构造
        self.obj = Count()

    def test_add(self):
        print self.obj.add(10, 20) == 30

    def test_sub(self):
        print self.obj.sub(10, 5) == 12

    def tearDown(self):
        # 析构
        self.obj = None


if __name__ == '__main__':
    s = unittest.makeSuite(TestCount, prefix='test')
    print s.countTestCases()
    runner = unittest.TextTestRunner()
    runner.run(s)
```

# 高级用法
`unittest.main()` 自动检测测试类中所有以 test 开头的方法

使用这种方法时, 如果使用 IDLE 的话, 可能会出现下面这种错:

```
Traceback (most recent call last):
  File "C:\Users\chaoqun.zhu\Desktop\ss.py", line 32, in <module>
    unittest.main()
  File "C:\Python27\lib\unittest\main.py", line 95, in __init__
    self.runTests()
  File "C:\Python27\lib\unittest\main.py", line 231, in runTests
    sys.exit(not self.result.wasSuccessful())
SystemExit: False
```

这是一个 IDLE 问题

因为 `unittest.main()` 函数会调用 `sys.exit()` 来结束函数进程, 类似 `sys.exit(1)` 表示非正常退出, `sys.exist(0)` 表示正常退出;

可以使用 `unittest.main(exit=False)` 解决.

**main() 方法过程:**

![](http://i65.tinypic.com/jh3pzd.jpg)

# 结果判断
继承 `unittest.TestCase` 就会继承下面这些方法

| assert                   | condition                                                                  |
| ------------------------ | -------------------------------------------------------------------------- |
| assertEqual(a, b)        | a == b<br>如果 a == b 则测试通过;<br>如果 a != b 则发生断言;<br>下面的类推 |
| assertNotEqual(a, b)     | a != b                                                                     |
| assertTrue(x)            | x is True                                                                  |
| assertFalse(x)           | x is False                                                                 |
| assertIs(a, b)           | a is b                                                                     |
| assertNot(a, b)          | a not b                                                                    |
| assertIsNone(x)          | x is None                                                                  |
| assertIsNotNone(x)       | x is not none                                                              |
| asertIn(a, b)            | a in b                                                                     |
| assertNotIn(a, b)        | a not in b                                                                 |
| assertIsInstance(a, b)   | instance(a, b)                                                             |
| assertNoIsInstance(a, b) | not instance(a, b)                                                         |

<br>

```python
# -*- coding: utf-8 -*-
import unittest


class Count(object):

    def add(self, x, y):
        return x + y

    def sub(self, x, y):
        return x - y


class TestCount(unittest.TestCase):

    def setUp(self):
        # 构造
        self.obj = Count()

    def test_add(self):
        self.assertEqual(self.obj.add(10, 20), 0)

    def test_sub(self):
        self.assertEqual(self.obj.sub(10, 5), 5)

    def tearDown(self):
        # 析构
        self.obj = None


if __name__ == '__main__':
    unittest.main(exit=False)
```

执行结果:

```
F.
======================================================================
FAIL: test_add (__main__.TestCount)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "C:\Users\chaoqun.zhu\Desktop\ss.py", line 21, in test_add
    self.assertEqual(self.obj.add(10, 20), 0)
AssertionError: 30 != 0

----------------------------------------------------------------------
Ran 2 tests in 0.000s

FAILED (failures=1)
```

第一行的 F 表示有几个 FAIL, 这里只有一个.

# 忽略测试
```
@unittest.skip(reson)
@unittest.skip(condition, reason)
@unittest.skipUnless(condition, reason)
@unittest.expectedFailure  失败不计入失败结果
```


```python
@unittest.skip('Not Test')
def test_sub(self):
    self.assertEqual(self.obj.sub(10, 5), 5)


@unittest.expectedFailure
def test_sub(self):
    self.assertEqual(self.obj.sub(10, 5), 5)
```

# 解决环境依赖 mock
向测试对象提供一套和测试资源完全相同的接口和方法, 不关心具体实现过程, 只关心具体结果.

```
py 3.0 +
unittest.mock
```


```
py 2.7<br>
pip install mock
mock.Mock
```


关于 mock 参考: [使用 Python Mock 类进行单元测试](http://www.oschina.net/translate/unit-testing-with-the-python-mock-class)

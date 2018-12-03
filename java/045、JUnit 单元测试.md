References:<br>
[在 Eclipse 中使用 JUnit4 进行单元测试 (初级篇)](http://blog.csdn.net/andycpp/article/details/1327147/)<br>
[在 Eclipse 中使用 JUnit4 进行单元测试 (中级篇)](http://blog.csdn.net/andycpp/article/details/1327346)<br>
[在 Eclipse 中使用 JUnit4 进行单元测试 (高级篇)](http://blog.csdn.net/andycpp/article/details/1329218)<br>
[1、junit 学习之 junit 的基本介绍](http://huihai.iteye.com/blog/1986568)<br>
[2、junit 学习之 hamcrest、testSuite 介绍及测试原则](http://huihai.iteye.com/blog/1994270)

在编写大型程序的时候, 需要写成千上万个方法, 应该能确保每一个方法都完全正确, 所以说, 每编写完一个方法之后, 都应该对这个方法的方方面面进行测试.

最简单的方法当然是直接在 main 方法里面直接调用要测试的方法就行了, 但这在有多个方法要测试时就显得麻烦了, 假设你要测试 10 个方法, 那就需要在 main 里依次更换要测试的方法.

那有没有什么办法能不这么频繁的改动 main 方法又能测试完所有要测试的方法呢?

利用 **JUnit** 就能做到这一点, 具体的 JUnit 使用方法参考上面的 **References**, 这里教大家一种简单点的使用方法.

在你要测试的方法前加上 `@Test` 就可以了.

```java
public class App {

    @Test
    public void test_test() {
        System.out.println("8888");
    }
}
```

这里 Eclipse 就会提示错误, 我们引入 JUnit 包就可以了.

![](http://i66.tinypic.com/ejbk3q.jpg)

测试方法也很简单, 如果想测试**指定的方法**, 那就在要测试的方法名上右键, 选择 Run As 或者 Debug As \-\> JUnit, 如果要测试**所有的方法**, 那就在类的作用域里右键, 选择 Run 或者 Debug \-\> JUnit.

或者在 JUnit 的窗体内也可以操作.

![](http://i67.tinypic.com/oupef8.jpg)

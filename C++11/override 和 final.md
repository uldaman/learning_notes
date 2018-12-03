在 国嵌C++ 中曾分析过重载与重写.

看下面这个例子:

```cpp
class CParent {
public:
    virtualvoid func() {
        cout << "void func ()" << endl;
    }
};

class CChild
: public CParent {
public:
    CChild(){}

    virtualvoid func(int a, int b) {
        cout << "func (int a, int b)"  << endl;
    }
};
```

子类中的 `func(int a, int b, int c)` 函数和父类中的 `func()` 函数是什么关系? 答案是重载.

在C++11以前, 很容易让你在本想重写基类某个虚函数的时候却意外地创建了另一个虚函数.

并且, 由于子类中的 vitual 关键字是可选的, 这使得阅读代码变得很费劲.

上叙的这两点都让确定子类中的某个方法是否是虚函数变得困难, 因为需要追溯到继承体系的源头才能搞清楚.

C++11引入了新的语法来解决这个问题:

**override** 这个特殊的标识符意味编译器将去检查基类中有没有一个具有相同签名的虚函数, 如果没有, 编译器就会报错!
简单的说, 就是表示当前函数是重写基类虚函数的.

```c++
class Base {
    virtualvoid some_func(float);
};

class Derived
    : public Base {
    // 报错, override 表示 some_func 应当是重写基类中的虚函数, 而基类却没有此类型的函数
    virtualvoid some_func(int) override;
}
```

****

**final** 和 override 相反, 它是防止基类被继承和防止子类重写基类虚函数的.
即, 如果 final 修饰类, 则此类不能被继承, 如果修饰函数, 则类函数不能被重写, 就算有 virtual 关键字都不行.

```cpp
class Base1 final {

};

class Derived1
    : public Base1 {

}; // 报错, 因为类 Base1 被标记为 final 了

class Base2 {
    virtualvoid f() final;
};

class Derived2
    : public Base2 {
    void f(); // 报错, 因为虚函数 Base2::f 被标记为 final 了.
}
```

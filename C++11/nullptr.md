先看下面这两个重载函数.

```c++
    void foo(char *);
    void foo(int);
```

C++中用 NULL 来表示空指针, 而 NULL 被定义为 0: #define NULL 0.
那么 foo(NULL); 语句将会调用 foo(int), 这几乎必定不是程序员想要的, 也不是代码直观上要表达的意图(因为我们实际想要传递的是一个空指针).

C++11通过引入一个新的关键字 **nullptr** 充当单独的空指针常量来纠正这个问题.
它的类型是 nullptr_t, 是一个可以隐式转换成**任意类型的指针**或**指向成员的指针**的类型,并且可以和这些类型进行比较.
但它**不能隐式转换为整型**, 也不能与整型做比较, bool类型除外(可以隐式转换为bool型, 取值为false).
为了向下兼容,0仍然是一个有效的空指针常量!

```c++
    char *pc = nullptr;     // OK
    int  *pi = nullptr;     // OK
    bool   b = nullptr;     // OK. b is false.
    int    i = nullptr;     // error

    foo(nullptr);           // calls foo(char *), not foo(int);
```

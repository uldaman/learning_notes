在说 move 之前有必要重新再认识下 C++ 98/03 中的 lvalues 和 rvalues, 即左值和右值.

**首先
对左值和右值的一个最常见的误解是：等号左边的就是左值，等号右边的就是右值;
这是错误的, 我们应该时刻谨记 lvalue 和 rvalue 都是针对表达式而言.**
lvalue 是指那些单一表达式结束之后依然存在的持久对象. 例如: obj, *ptr, prt[index], ++x 等;
rvalue 是指那些表达式结束时就不复存在的临时对象. 例如: 常数 1729, x + y, x++ 等.

当我们写 int x = 10; 时, x 是一个 lvalue, 因为当这个表达式结束时, x 依然存在, 它代表的是一个持久对象.
当我们写 int i = x + y; 时, x + y 是一个 rvalue, 因为当执行这句表达式时, 其实这里应该是:

```c++
int temp = x + y;
int i = temp;
```

而这句表达式结束时, temp 就被释放掉了..

**再解释下 ++x 和 x++ 的区别.**
++x 是一个 lvalue, 因为它将直接修改 x 的值, 并返回原来的 x, 它代表的还是原来那个持久对象.
而 x++ 却是一个 rvalue, 因为执行这句表达式时, 它其实是下面这样的:

```c++
int temp = x;
x = x + 1;
return temp;
```

也就是说, 它先拷贝一份持久对象的初值, 再修改持久对象的值, 最后返回刚才拷贝的临时对象.

所以说, **lvalue 与 rvalue 之分的关键就在于表达式代表了什么(是持久对象还是临时对象).
**例如 ++x 代表的是那个持久对象本身, 而 x++ 代表的是那个拷贝的临时对象.

另一个判断一个表达式是不是 lvalue 的方法是看能不能对它取址, 如果可以, 那就是一个 lvalue, 反之就是一个 rvalue.
C++ 中规定, 取址操作要求它的操作数必须是一个 lvalue.
这是因为对一个持久对象取址是 OK 的, 但是对一个临时对象取址是极端危险的, 因为临时对象很快就会被销毁.

对于一个函数来说, **当且仅当它的返回值是一个引用的时候, 它才是 lvalue.**
例如, 给定语句 `vector<int> v`, v[n] 是一个 lvalue, 因为 `[]()` 返回的是一个引用, 这里是 int&, 用上面的取址判断也是OK的, &v[n] 完全没有问题.
而给定语句 string s、string t, s + t 是一个 rvalue, 因为 +() 返回的是一个新的string(参考前面的 x + y), 并且 &(s + t) 也是非法的.

**常量引用与**非**常量引用
**lvalue 和 rvalue 都有常量和非常量之分., 看下面的例子:

```c++
string one("1"); // 非常量左值

const string two("2"); // 常量左值

string three() { // 非常量右值
    return "3"
}

const string four() { // 常量右值
    return "4"
}
```

一个引用 Type& 可以绑定到非常量 lvalue(可以通过引用来读取和修改原来的值);
但不能绑定到 const lvalue, 因为那将违背 const 的正确性;
也不能绑定到非常量 rvalue, 因为 rvalue 是一个临时对象, 当你通过引用来修改它的值的时候, 而临时对象早就已经不存在了, 结果可想.
更不能绑定到 const rvalue, 因为那将同时违背上面的两点…

而一个常量引用 const Type& 却可以同时绑定到 非常量 lvalue、const lvalue、非常量 rvalue 和 const rvalue. 因为 const Type& “只能看, 不能改..”.

这个时候, 可能会出现疑问**”既然 rvalue 本身就是不能被修改的, 那 const rvalue 和 非常量 rvalue 又有什么不同呢?”**
区别在于, 在 C++ 98/03 中, 非常量 rvalue 可以手动调用 non_const 成员函数来修改值; 在 C++11 中, 答案有了显著的变化, 它能用来实现 move 语意..

OK, 那什么是 mvoe 语义? 简单的说就是, **move 语义允许修改右值.**

C++11加入了**右值引用**(rvalue reference)的概念(**用 && 标识**), 它被来区分对左值或右值的引用.

C++98/03 性能上被长期诟病的问题之一, 就是其耗时且不必要的深拷贝.
例如, 如果创建了或是从函数返回了一个 string 类型的临时对象, 要将其保存起来只能通过生成新的 string 对象并且把原临时对象所有的数据复制进去, 该临时对象和其拥有的內存会被销毁.(为方便理解, 这里忽略了编译器的返回值优化).
什么意思呢? 假设你有一大堆 string 像这样的：

```c++
    string s0("0");
    string s1("1");
    string s2("2");
    string s3("3");
    string s4("4");
    // 然后你想像这样把它们串接起来：
    string dest = s0 + " " + s1 + " " + s2 + " " + s3 + " " + s4;
```

我们已经知道 +() 会返回一个临时的对象, 所以上面的串接动作就因此产生了 8 个临时对象. 不管怎么说, 这都是很浪费性能的一件事.

在 C++11中, std::sting 新添了"移动构造函数", 对某个 string 的**右值引用可以单纯地从右值复制其内部数据的指针**到新的 string 中，然后将右值中的指针置空.
因为这个临时对象不会再被使用, 没代码会再访问这个空指针, 而且因为这个临时对象的内部指针是NULL, 所以当这个临时对象离开作用域时它的内存也不会被释放掉.
所以, 这个操作不仅没有代价高昂的深拷贝, 还是安全的,对用户不可见的!
这个操作不需要内存的复制, 而且空的临时对象的析构也不会销毁内存, 返回临时对象的函数只需要返回 std::string&& 就可以了. 如果 string 没有 move 构造函数, 那么就会调用常规拷贝构造函数, 如果有, 那么就会优先调用 move 构造函数, 这能够避免大量的内存分配和内存拷贝操作.

返回值类型为 std::string 的函数返回了一个 std::string 类型的临时对象, 这样的临时对象会被自动当作右值引用, 而不需要显示地将返回值类型改为 std::string&&.

对于 stl 中常用数据结构, C++11 已经默认加了 move 构造函数 和 重载了 = 运算符, 但是如果要给我我们自己写的类添加 move 语义已经怎么做呢?
答案是配合模板函数 `std::move<T>()` , 这个函数将一个数值强制转化为右值, 具体做法如下:

```c++
template <typename T>
class Buffer {
    std::string          _name;
    size_t               _size;
    std::unique_ptr<T[]> _buffer;

public:
    // move constructor
    Buffer(Buffer&& temp):
        // temp._name 本身是 lvalue, 使用 std::move 后转化为 右值, 所以这里将触发 std::string 的 move 构造函数.
        _name(std::move(temp._name)),
        _size(temp._size),
        _buffer(std::move(temp._buffer)) {
        temp._buffer = nullptr;
        temp._size = 0;
    }

    // move assignment operator
    Buffer& operator=(Buffer&& temp) {
        assert(this != &temp); // assert if this is not a temporary

        _buffer = nullptr;
        _size = temp._size;
        _buffer = std::move(temp._buffer);

        _name = std::move(temp._name);

        temp._buffer = nullptr;
        temp._size = 0;

        return *this;
    }
};
```

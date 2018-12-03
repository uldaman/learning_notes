这之前我们遍历一个数组或者一个list之类的容器, 需要写很多代码, 而其它语言实现支持"糖块句法", 允许程序通过一个简单的"foreach"语句自动遍历list中的元素.
C++11增加了一个类似的特性, for语句可以简单地遍历列表中的元素.

```c++
int my_array[5] = {1, 2, 3, 4, 5};
// double the value of each element in my_array:
for (int &x : my_array) {
     x *= 2;
}
```

这种形式的for语句叫作"基于范围的for语句", 它会遍历列表中的每一个元素.

**请注意上面的 & 符号, 如果想要通过 x 更改数组中的值就必须要使用引用类型, 否则 x 是一个拷贝的值(就像参数的参数一样).**

可以用在C风格数组, 初始化列表和那些带有能返回迭代器的begin()和end()函数的类型上.

所有提供了begin/end的标准容器都可以使用基于范围的for语句.

```c++
std::map<std::string, std::vector<int>> map;
std::vector<int> v;
v.push_back(1);
v.push_back(2);
v.push_back(3);
map["one"] = v;

for(const auto& kvp : map) {
  std::cout << kvp.first << std::endl;

  for(auto v : kvp.second) {
     std::cout << v << std::endl;
  }
}

int arr[] = {1, 2, 3, 4, 5};
for(int& e : arr) {
  e = e*e;
}
```

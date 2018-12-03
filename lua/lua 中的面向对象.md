Lua 中本身并没有类这种数据结构, 但是可以使用 table 来模拟面向对象中的类, 如下所示:

```lua
People = {}
People.sayHi = function()
  print("Hi People!")
end
```

或者可以这样:

```lua
function People.sayHi()
   print("Hi People!")
end
```

这就相当于创建了一个 People 类, 那怎么生成这个类的实例呢? 它的构造函数呢? 往下看:

```lua
People.new = function()
  local self = {}
  for key, var in pairs(People) do
    self[key] = var
  end
  return self
end

local p1 = People.new()
p1.sayHi()
```

我们还可以为这个类添加带参数的构造函数:

```lua
People.new = function(name)
  local self = {}
  self.name = name;
  for key, var in pairs(People) do
    self[key] = var
  end
  return self
end

local p2 = People.new("李四")
print(p2.name)
```

现在, 我们类可以通过 new() 方法来实例化了.

新问题是, 我们都知道, C++ 类有个 this 指针, 这点, 又要怎么实现呢? 可以借助 Lua 的王法糖 (冒号):

```lua
People.sayHello = function(this)
  print(this.name .. " Say Hello!")
end

local p3 = People.new("張三")
p3:sayHello()
```

this 指针現在也有了, 那 C++ 类可以继承, Lua 中又怎么实现呢?

```lua
Man = {}
Man.new = function(this)
  local self = People.new()
  for key, var in pairs(Man) do
    self[key] = var
  end
  return self
end
```

这里先生成一个 People "对象", 然后把这个对象 Key 对应的 value 替换成 Man 中 Key 对应的 value, 这样的话,  如果 Man 中没有 "重写" People 中的方法, 那么, 返回的 self 中的方法还是为 People 中的方法, 如果 "重写" 了 People 中的方法, 因为做了替换操作, 所以返回的 self 中的方法其实是对应的 Man 中的方法.

然后, 我们来重写下 sayHi() 方法:

```lua
Man.sayHi = function()
  print("Hi Man")
end
```

不管怎么样, 我们现在已经可以实现多态了:

```lua
sayHi = function(tab)
  tab.sayHi()
end
sayHi(m)
```

> 勘误: 之前提到了 Lua 中的语法糖, 其实 Lua 是有个默认 self 参数的, 和 this 指针一样, 具体參考: [http://blog.csdn.net/stormbjm/article/details/38532413](http://blog.csdn.net/stormbjm/article/details/38532413)

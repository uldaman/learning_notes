# 闭包
对于习惯了 C/C++ 这种不可嵌套定义函数的语言来说, 理解闭包真不太容易;

要解释闭包就要先理解个小知识点:<br>
Lua 中的函数是带有词法定界（lexical scoping）的**第一类值** (first-class values).<br>
第一类值（first-class values）是指, 函数和其他值（数值、字符串）一样, 可以被存放在变量中, 也可以存放在表中, 可以作为函数的参数, 还可以作为函数的返回值.

正因为函数也可以被嵌套定义在另一個函数內部, 假设函数 f2 定义在函数 f1 中, 那么就称 f2 为 f1 的内嵌 (inner) 函数, f1 为 f2 的外包 (enclosing) 函数, 外包和内嵌都具有传递性, 即 f2 的内嵌必然是 f1的内嵌, 而 f1 的外包也一定是 f2 的外包, 内嵌函数能访问外包函数已创建的所有局部变量, 这种特性便是所谓的词法定界 (lexical scoping), 而这些局部变量则称为该内嵌函数的外部局部变量 (external local variable) 或 supvalue.

简单的说闭包是一个函数加上它可以正确访问的 upvalue, 这个函数定义在另一个函数内部, 并且可以访问定义在外部函数内的成员变量、参数、以及全局函数, 如:

```lua
function f1(n)
  -- 函数参数也是局部变量
  return function ()
    print(n) -- 引用外包函数的局部变量
  end
end
```

对闭包有兴趣的可以看看这篇网文: [http://www.cnblogs.com/yyxt/p/3875185.html](http://www.cnblogs.com/yyxt/p/3875185.html)

# 通过闭包来模拟面向对象
上一篇笔记, 通过 table 模拟了面向对象, 这一次, 通过闭包来模拟.

```lua
local function People()
  local tab = {}

  function tab:sayHi()
    print("Say Hi!")
  end

  return tab
end

local p = People()
p.sayHi()
```


再來看看怎麼继承:

```lua
local function Man()
  local tab = People()  -- "继承" People

  function tab:sayHi()
    print("Man Say Hi!")
  end

  return tab
end

local m = Man()
m.sayHi()
```

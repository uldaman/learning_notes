# Mongodb Cursor

## 什么是游标

游标是数据库系统为用户开设的一个数据缓冲区, 用户可以逐一从游标中获取记录, 这相当于一种__懒加载__形式, 当通过 __find()__ 获取数据时, 并没有真正的去查询数据库, 而是返回一个游标, 只要当用到的时候(也就是遍历游标的时候)才会到数据库中将数据取出来, 其操作方法有点类似__迭代器__.

游标主要是为了达到延长执行的效果, 客户端通过对游标进行一些设置就能对查询结果进行有效地控制, 如: 可以限制查询得到的结果数量、跳过部分结果、或对结果集按任意键进行排序等!

直接对一个集合调用 __find()__ 方法时, 如果查询结果超过二十条, 只会返回二十条的结果, 这是因为 Mongodb 会自动递归 find() 返回的游标:

![](http://i63.tinypic.com/2uy3a6t.jpg)

## 游标基础使用

当使用一个变量来保存 __find()__ 的返回值时, 就会获取到该查询动作的 Cursor.

```
var cursor = db.num.find()
```


- cursor.count(): 获取查询到的结果数量
- cursor.hasNext(): 是否还有数据
- cursor.next(): 取得数据
- cursor.objsLeftInBatch(): 还剩多少条结果, 即还可以迭代几次
- cursor.toArray(): 将游标转换成数组, 然后就可以通过下标来访问数据, 如 cursor.toArray()[0]

## limit, skip, sort

我们已经知道了 __find()__ 方法其实返回的是游标, 所以 limit, skip sort 这些方法其实是操作游标的...

- limit: 限制游标返回的数量, 指定了上限
- skip: 忽略前面的部分文档, 如果文档总数量小于忽略的数量, 则返回空集合
- sort: 得到的子集合进行排序, 可以按照多个键进行正反排序

```
var cursor = db.num.find()
cursor.sort({"var": -1})

var cursor = db.num.find()
cursor.limit(5)

var cursor = db.num.find()
cursor.limit(5).skip(3)
```

## forEach 方法

__forEach()__ 方法用来遍历游标, 并且可以提供一个 __function__ 做为回调函数, 在遍历时, 将传入的 function 依次作用到 Cursor 中的每条数据上.

```
cursor.forEach(fuction(doc) {print(doc.name)})
'张三'
'李四'
'王五'
```

## map 方法

__map()__ 方法功能与 __forEach()__ 一致, 但它可以接受返回值, 并把返回结果放到一个数组中.

```
cursor.map(fuction(doc) {return doc.name})
['张三', '李四', '王五']
```

## 更多

关于游标的更多操作, 参考[官方文档](https://docs.mongodb.org/manual/reference/method/js-cursor/)

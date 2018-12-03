# 1. 概述
MapReduce 是 Mongodb 中的高级聚合工具, count、distinct、group 能做的事 MapReduce 都能做.

除此外, 它还可以并行化到多个服务器上.<br>
它将聚合运算拆分, 再将各个部分发送到不同的服务器上, 让每台服务器都完成一部分, 当所有服务器都完成的时候, 再把结果汇集起来形成最终完整的结果.

MapReduce 十分强大, 但其代价就是牺牲性能, 所以千万不要用在"实时"的操作中, 可以作为后台任务来运行 MapReduce, 将结果缓存在另一个集合中, 当"实时"操作发生时, 我们从这个缓存集合中进行查询.

# 2. 实例
MapReduce 直接解释起来可能比较麻烦, 通过一个实例来学习是最好不过了, 这里参考别人的文章, 是的, 这个实例, 我只是个搬运工...[原文传送门](http://www.cnblogs.com/loogn/archive/2012/02/09/2344054.html)

语法:

```js
db.runCommand({
    mapreduce: // 字符串, 集合名,
    map: // 函数, 映射函数, 具体见下文
    reduce: // 函数, 简化函数, 具体见下文
    [query: ] // 文档, 发往map函数前先给过渡文档
    [sort: ] // 文档, 发往map函数前先给文档排序
    [limit: ] // 整数, 发往map函数的文档数量上限
    [out: ] // 字符串, 统计结果保存的集合
    [keeptemp: ] // 布尔值, 链接关闭时临时结果集合是否保存
    [finalize: ] // 函数, 将reduce的结果送给这个函数, 做最后的处理
    [scope: ] // 文档, js代码中要用到的变量
    [jsMode: ] // 布尔值, 是否减少执行过程中 BSON 和 JS 的转换, 默认 true
    // 注：
    // false 时 BSON-->JS-->map-->BSON-->JS-->reduce-->BSON, 可处理非常大的 mapreduce
    // true 时 BSON-->js-->map-->reduce-->BSON
    [verbose: ] 布尔值, 是否产生更加详细的服务器日志, 默认 true
});
```

测试数据:

![](http://i68.tinypic.com/1zd81p2.jpg)

现在我要统计同一 __age__ 的 __name__, 也就是像如下的结果:

```js
{age: 0, names: ['name_6', 'name_12', 'name_18']}
{age: 1, names: ['name_1', 'name_7', 'name_13', 'name_19']}
......
```

__第一步__是写 __Map__ (映射)函数, 可以简单的理解成分组吧~

```js
var m = function(){
    emit(this.age, this.name);
}
```

__emit__ 的第一个参数是 __key__, 就是分组的依据, 参考 __group__ 中的 __key__ 的功能, 这是自然是 __age__ 了, 后面一个是 __value__, 就是要统计的数据, 可以是 JSON 对象. __this__ 表示的是对当前文档的引用.

这样 __m__ 就会把送过来的数据根据 __Map__ 函数中定义的 __key__ 进行分组了, 组中的元素就是 __Map__ 函数中定义的 __value__, 可以想象成如下结构:

```js
// 第一组
{key: 0, values: ['name_6', 'name_12', 'name_18']}

// 第二组
{key: 1, values: ['name_1', 'name_7', 'name_13', 'name_19']}
```

组中的 __key__ 其实就是 __age__ 的值了, __values__ 是个数组, 数组内的成员都有相同的 __age__ !!

__第二步__就是简化了, 编写 __reduce__ 函数:

```js
var r = function(key, values){
    var ret = {age: key, names: values};
    return ret;
}
```

__reduce__ 函数会处理每一个分组, 参数就是我们想像分组里的 __key__ 和 __values__.

这里 __reduce__ 函数只是简单的把 __key__ 和 __values__ 包装了一下, 然后返回一个对象, 对象结构正好和我们想象的相符:

```
{age: 对应的 age，names: [名字1，名字2..]}
```

__然后__, 还可以编写 __finalize__ 函数对 __reduce__ 的返回值做最后处理:

```js
var f = function(key, rval){
    if(key == 0){
        rval.msg = "A new life, Baby!";
    }
    return rval;
}
```

这里的 __key__ 还是上面的 __key__, 也就是还是 __age__, __rval__ 是 __reduce__ 的返回值, 所以 __rval__ 的一个实例如: __{age: 0, names: ['name\_6', 'name\_12', 'name\_18']},__

这里判断 __key__ 是不是 0, 如果是, 就在 __rval__ 对象上加 __msg__ 属性, 显然也可以判断 __rval.age == 0__, 因为 __key__ 和 __rval.age__ 是相等的嘛~~

__最终__, 就是调用 __MpaReduce__ 工具了:

```
db.runCommand({
    mapreduce: 't',
    map: m,
    reduce: r,
    finalize: f,
    out: 't_age_names'
    }
)
```

最终返回的 __t\_age\_names__ 集合中的文档包含两个键 __\_id__ 和 __value__:

- __\_id__, 保存上面的 key, 这个例子中就是 age
- __value__, 保存最终运算出的结果, 如 {'age': 3, 'name': ['name\_1', 'name\_2']}

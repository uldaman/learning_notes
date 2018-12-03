参考资料:<br>
[用 Python 做科学计算](http://old.sebug.net/paper/books/scipydoc/index.html)<br>
[NumPy - 快速处理数据](http://old.sebug.net/paper/books/scipydoc/numpy_intro.html)

[TOC]

# NumPy 介绍
NumPy 是 Python 的一个科学计算的库, 主要提供了矩阵运算的功能 (说白了就是维护对象是同种元素的多维数组), 在 NumPy 中维度 (dimensions) 叫做轴 (axes), 轴的个数叫做秩 (rank);<br>
比如说, 二维数组相当于是两个一维数组 (一个数组 a 中是另一个数组 b 的元素), 那第一轴指的就是上层的那个数组 b, 第二个轴指的是上层数组里的元素数组 a (简单的说就是第二轴是第一轴的元素), 轴的个数叫做秩, 所以这个例子的秩就等于 2.

NumPy 提供了两种基本的对象:

- ndarray (N-dimensional array object), (下文统一称之为数组) 存储单一数据类型的多维数组
- ufunc (universal function object), 是能够对 ndarray 进行处理的函数


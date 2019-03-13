**sorted 函数**

```python
>>>pp = [('Leborn James', 98), ('Kevin Durant', 97), ('James Harden', 96), ('Stephen Curry', 95), ('Anthony Davis', 94)]
>>>sorted(pp)
```

得到：

```python
[('Anthony Davis', 94), ('James Harden', 96), ('Kevin Durant', 97), ('Leborn James', 98), ('Stephen Curry', 95)]
```

**isinstance 函数**：接受两个参数，作用是判断数据的数据类型。

用法如下：

```python
>>> isinstance(1, int)
True
>>> isinstance(2.2, float)
True
>>> isinstance('hello', int)
False
>>> isinstance('hello', float)
False
>>> isinstance('hello', str)
True
```

**filter 函数**的使用格式：

```python
filter(a, b)
# a 为函数，b 为被处理的数据列表
# a 会对 b 中的每个元素进行判断，结果为真则保留，否则舍弃
```

现在举例说明 `filter` 函数的用法。 我们要对一个列表进行过滤，保留其中的 `int` 类型的元素，舍弃其它数据类型的元素：

```python
# 被处理的列表 b
>>> b = [9, 'Python', True, 3.14, 2018, -4, abs]
# 作为 filter 函数的第一个参数、用来决定元素去留的函数 a

>>> def a(i):
...     if isinstance(i, int):
...         return True
...     else:
...         return False
... 
```

现在我们使用 filter 函数来得到想要的结果：

```python
>>> filter(a, b)
<filter object at 0x7f00bd69b198>
```

有点奇怪，得到这样一个结果~ 请仔细看这行打印信息，它指的是一个 filter 对象，也就是说，`filter` 函数的返回值是一个 filter 对象，并不像前面讲的 `sorted` 函数，返回的结果就是列表。我们可以使用 `list` 方法将 `filter` 函数的返回值转换为列表，结果就是这样：

```python
>>> list(filter(a, b))
[9, True, 2018, -4]
```

结果和我们期望的差不多，过滤掉了 float、字符串、函数，值得注意的是 `True` 这一项被保留下来，因为在判断数值时，`True` 和 1 是等值，`False` 和 0 是等值，所以它们也就属于 `int` 类型了：

```python
>>> True == 1
True
>>> False == 0
True
```

**map 函数**

`map` 函数接受两个参数，格式和 `filter` 一样：

```python
map(a, b)
# a 是函数，用来处理 b 参数
# b 是可迭代对象
```

**reduce 函数**

`reduce` 函数的使用格式依然如此：

```python
reduce(a, b)
# a 函数用来处理 b 
# b 是可迭代对象
```

与 `map` 不同的是，`reduce` 的 a 函数对 b 序列做积累处理，我们来举例说明。 同样的 b 列表：

```python
>>> b = [1, -2, 3, -4, -5, 6, 7, 8, -9]
```

现在我们要对 b 列表中的元素进行求和，即实现 `sum(b)` 的结果，定义 a 函数：

```python
>>> def a(x, y):
...     return x + y
... 
```

需要注意的一点：`reduce` 在 python2 中同 `map` 函数一样可以直接使用，在 python3 中需要从 `functools` 库中引入：

```python
>>> from functools import reduce
```

现在可以使用 `reduce` 函数对 b 列表进行求和了，它的返回值是一个确定的数值，与 `sum(b)` 的结果一样：

```python
>>> reduce(a, b)
5
>>> sum(b)
5
```

**lambda 函数**

Python 通过 `lambda` 提供了对匿名函数的支持，使用方法很简单，看下面的例子：

```python
>>> double = lambda x: x * 2
>>> double(5)
10
```

上面的例子中 double 这个变量其实就是一个匿名函数，使用的时候直接 `double(x)` 就会执行函数。

例子中使用 `lambda` 定义了一个匿名函数。 `lambda` 返回值时不需要 `return` 。

`lambda` 函数通常用在需要传入一个函数作为参数，并且这个函数只在这一个地方使用的情况下，匿名函数一般会作为一个参数传递，冒号前面是参数，后面是返回值。它的好处是没有函数名，可以避免变量冲突，限制是只能有一个表达式。

**partial 函数**

![partial 函数](https://github.com/wannain/python/blob/master/%E5%A5%87%E6%B7%AB%E6%8A%80%E5%B7%A7/partial.jpg)

**切片 slice**

切片用于获取一个序列（列表或元组）或者字符串的一部分，返回一个新的序列或者字符串，使用方法是中括号中指定一个列表的开始下标与结束下标，用冒号隔开，切片在先前的实验中讲解字符串的时候有介绍过，不只是字符串，列表或元组使用切片也非常常见。

**列表解析**（list comprehension），也称为列表推导。

是从 Python 2.0 就被添加进来的新特性，提供了一种简单优雅的方式操作列表元素，看下面一个例子：

```python
>>> numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
# 获取 numbers 中的所有偶数
>>> [x for x in numbers if x % 2 == 0]
[2, 4, 6, 8, 10]
# 对 numbers 的每个数求平方
>>> [x * x for x in numbers]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

**字典解析**

处理的对象是字典中的 key 和 value。

```python
>>> d = {'a': 1, 'b': 2, 'c': 3}
>>> {k:v*v for k, v in d.items()}
{'a': 1, 'b': 4, 'c': 9}
```

需要注意的是字典是不能被迭代的，需要使用字典的方法 `items()` 把字典变成一个可迭代对象。



# 后续补充

**迭代器：一个个读取元素的对象**

**生成器：只能被迭代一次的迭代器**

**yield：返回生成器的 return**

**装饰器：对函数进行修饰**

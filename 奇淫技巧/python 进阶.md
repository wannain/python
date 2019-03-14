# python 进阶

## 参数

1. 自调用

如果执行该脚本的时候，该 if 判断语句将会是 True,那么内部的代码将会执行。 如果外部调用该脚本，if 判断语句则为 False,内部代码将不会执行。

```python
if __name__=='__main__':
    #code here
```

2. 可变参数

参数 *grades 使用了 * 修饰，表明该参数是一个可变参数，这是一个可迭代的对象。该函数输入姓名和各科的成绩，输出姓名和总共成绩。所以可以这样调用函数 report('Mike', 8, 9)，输出的结果为 Mike total grade is 17, 也可以这样调用 report('Mike', 8, 9, 10)，输出的结果为 Mike total grade is 27

```python
def report(name, *grades):
    total_grade = 0
    for grade in grades:
        total_grade += grade
    print(name, 'total grade is ', total_grade)
```

3. 关键词参数

关键字参数可以传入 0 个或者任意个含参数名的参数，这些参数名在函数定义中并没有出现，这些参数在函数内部自动封装成一个字典(dict)

```python
def portrait(name, **kw):
    print('name is', name)
    for k,v in kw.items():
        print(k, v)
```

## 变量

1. 局部变量

这里不介绍

2. 全局变量

首先我们在外部定义一个全局变量 a=None, 然后再 fun() 中声明 这个 a 是来自外部的 a 。声明方式就是 global a 。 然后对这个外部的 a 修改后, 修改的效果会被施加到外部的 a 上。 所以我们将能看到运行完 fun(), a 的值从 None 变成了 20。

```python
APPLY = 100 # 全局变量
a = None
def fun():
    global a    # 使用之前在全局里定义的 a
    a = 20      # 现在的 a 是全局变量了
    return a+100

print(APPLE)    # 100
print('a past:', a)  # None
fun()
print('a now:', a)   # 20
```

## 读写文件

### \n 换行命令

### \t tab 对齐

使用 \t 能够达到 tab 对齐的效果

### open 读文件方式

使用 open 能够打开一个文件, open 的第一个参数为文件名和路径 ‘my file.txt’, 第二个参数为将要以什么方式打开它, 比如 w 为可写方式. 如果计算机没有找到 ‘my file.txt’ 这个文件, w 方式能够创建一个新的文件, 并命名为 my file.txt

```python
my_file=open('my file.txt','w')   #用法: open('文件名','形式'), 其中形式有'w':write;'r':read.
my_file.write(text)               #该语句会写入先前定义好的 text
my_file.close()                   #关闭文件
```

### 给文件增加内容

```python
append_text='\nThis is appended file.'  # 为这行文字提前空行 "\n"
my_file=open('my file.txt','a')   # 'a'=append 以增加内容的形式打开
my_file.write(append_text)
my_file.close()

""""
This is my first test.
This is the second line.
This the third line.
This is appended file.
""""
#运行后再去打开文件，会发现会增加一行代码中定义的字符串
```

### 读取文件内容 file.read()

使用 file.read() 能够读取到文本的所有内容

```python
file= open('my file.txt','r') 
content=file.read()  
print(content)

""""
This is my first test.
This is the second line.
This the third line.
This is appended file.    
""""
```

### 读取所有行 file.readline()

如果想在文本中一行行的读取文本, 可以使用 file.readline(), file.readline() 读取的内容和你使用的次数有关, 使用第二次的时候, 读取到的是文本的第二行, 并可以以此类推

```python
file= open('my file.txt','r') 
content=file.readline()  # 读取第一行
print(content)

""""
This is my first test.
""""
```

### 读取所有行 file.readlines()

如果想要读取所有行, 并可以使用像 for 一样的迭代器迭代这些行结果, 我们可以使用 file.readlines(), 将每一行的结果存储在 list 中, 方便以后迭代

```python
file= open('my file.txt','r') 
content=file.readlines() # python_list 形式
print(content)

""""
['This is my first test.\n', 'This is the second line.\n', 'This the third line.\n', 'This is appended file.']
""""

# 之后如果使用 for 来迭代输出:
for item in content:
    print(item)
    
"""
This is my first test.

This is the second line.

This the third line.

This is appended file.
"""
```

## list 列表补充

### list 添加

add append insert

### list 移除

remove delete

### list 索引

打印列表中的某个值的索引(index):

```python
a = [1,2,3,4,1,1,-1]
print(a.index(2)) # 显示列表a中第一次出现的值为2的项的索引
# 1
```

统计列表中的某值出现的次数

```python
a = [4,1,2,3,4,1,1,-1]
print(a.count(-1))
# 1
```

### list 排序

```python
a = [4,1,2,3,4,1,1,-1]
a.sort() # 默认从小到大排序
print(a)
# [-1, 1, 1, 1, 2, 3, 4, 4]

a.sort(reverse=True) # 从大到小排序
print(a)
# [4, 4, 3, 2, 1, 1, 1, -1]
```

## zip lambda map

### zip

zip 函数接受任意多个（包括 0 个和 1 个）序列作为参数，合并后返回一个 tuple 列表

### lambda

lambda 定义一个简单的函数，实现简化代码的功能，看代码会更好理解。

fun = lambda x,y : x+y, 冒号前的 x,y 为自变量，冒号后 x+y 为具体运算.

### map

map 是把函数和参数绑定在一起

```python
>>> def fun(x,y):
	return (x+y)
>>> list(map(fun,[1],[2]))
"""
[3]
"""
>>> list(map(fun,[1,2],[3,4]))
"""
[4,6]
"""
```

## copy & deepcopy 潜复制 & 深复制

### id

一个对象的 id 值在 CPython 解释器里就代表它在内存的地址

### 浅拷贝

使用浅拷贝时，python 只是拷贝了最外围的对象本身，内部的元素都只是拷贝了一个引用而已

### 深拷贝

deepcopy 对外围和内部元素都进行了拷贝对象本身，而不是对象的引用

### 直接赋值，潜复制，深复制的区别

深复制，即将被复制对象完全再复制一遍作为独立的新个体单独存在。所以改变原有被复制对象不会对已经复制出来的新对象产生影响。

赋值，并不会产生一个独立的对象单独存在，他只是将原有的数据块打上一个新标签，所以当其中一个标签被改变的时候，数据块就会发生变化，另一个标签也会随之改变。

浅复制要分两种情况进行讨论：

1）当浅复制的值是不可变对象（数值，字符串，元组）时和“等于赋值”的情况一样，对象的 id 值与浅复制原来的值相同。

2）当浅复制的值是可变对象（列表和元组）时会产生一个“不是那么独立的对象”存在。有两种情况：

第一种情况：复制的 对象中无 复杂 子对象，原来值的改变并不会影响浅复制的值，同时浅复制的值改变也并不会影响原来的值。原来值的 id 值与浅复制原来的值不同。

第二种情况：复制的对象中有 复杂 子对象 （例如列表中的一个子元素是一个列表），如果不改变其中复杂子对象，浅复制的值改变并不会影响原来的值。 但是改变原来的值 中的复杂子对象的值  会影响浅复制的值。

![参考链接](https://iaman.actor/blog/2016/04/17/copy-in-python)

## set 找不同

Set 最主要的功能就是寻找一个句子或者一个 list 当中不同的元素

```python
char_list = ['a', 'b', 'c', 'c', 'd', 'd', 'd']
sentence = 'Welcome Back to This Tutorial'
print(set(char_list))
# {'b', 'd', 'a', 'c'}
print(set(sentence))
# {'l', 'm', 'a', 'c', 't', 'r', 's', ' ', 'o', 'W', 'T', 'B', 'i', 'e', 'u', 'h', 'k'}
print(set(char_list+ list(sentence)))
# {'l', 'm', 'a', 'c', 't', 'r', 's', ' ', 'd', 'o', 'W', 'T', 'B', 'i', 'e', 'k', 'h', 'u', 'b'}
```

### 添加元素

定义好一个 set 之后我们还可以对其添加需要的元素, 使用 add 就能添加某个元素. 但是不是每一个东西都能添加, 比如一个列表

### 清除元素

清除一个元素可以用 remove 或者 discard, 而清除全部可以用 clear

```python
unique_char.remove('x')
print(unique_char)
# {'b', 'd', 'c', 'a'}
unique_char.discard('d')
print(unique_char)
# {'b', 'c', 'a'}
unique_char.clear()
print(unique_char)
# set()
```

### 筛选

进行一些筛选操作, 比如对比另一个东西, 看看原来的 set 里有没有和他不同的 (difference). 或者对比另一个东西, 看看 set 里有没有相同的 (intersection)

```python
unique_char = set(char_list)
print(unique_char.difference({'a', 'e', 'i'}))
# {'b', 'd', 'c'}
print(unique_char.intersection({'a', 'e', 'i'}))
# {'a'}
```

## 正则表达式

### 简单匹配

导入 re 内置模块

```python
import re
# regular expression
pattern1 = "cat"
pattern2 = "bird"
string = "dog runs to cat"
print(re.search(pattern1, string))  # <_sre.SRE_Match object; span=(12, 15), match='cat'>
print(re.search(pattern2, string))  # None
```

### 灵活匹配

使用 [] 将可能的字符囊括进来，而且在 pattern 的 “” 前面需要加上一个 r 用来表示这是正则表达式, 而不是普通字符串。

```python
# multiple patterns ("run" or "ran")
ptn = r"r[au]n"       # start with "r" means raw string
print(re.search(ptn, "dog runs to cat"))    # <_sre.SRE_Match object; span=(4, 7), match='run'>
```

同样, 中括号 [] 中还可以是以下这些或者是这些的组合. 比如 [A-Z] 表示的就是所有大写的英文字母. [0-9a-z] 表示可以是数字也可以是任何小写字母

```python
print(re.search(r"r[A-Z]n", "dog runs to cat"))     # None
print(re.search(r"r[a-z]n", "dog runs to cat"))     # <_sre.SRE_Match object; span=(4, 7), match='run'>
print(re.search(r"r[0-9]n", "dog r2ns to cat"))     # <_sre.SRE_Match object; span=(4, 7), match='r2n'>
print(re.search(r"r[0-9a-z]n", "dog runs to cat"))  # <_sre.SRE_Match object; span=(4, 7), match='run'>
```
---
title: Python高级特性
date: 2021-01-24 14:41:23
tags: Python
---

![](https://oss.forestyoung.top/aircraft-5336532_1920.jpg)

<!--more-->

## 切片

切片（Slice）操作符

```python
>>> L = ['Michael', 'Sarah', 'Tracy', 'Bob', 'Jack']

# 左包含，右不包含
>>> L[0:3]
['Michael', 'Sarah', 'Tracy']

# 第一个索引为0，可省略
>>> L[:3]
['Michael', 'Sarah', 'Tracy']

>>> L[1:3]
['Sarah', 'Tracy']

# L[-1]取倒数第一个元素
>>> L[-2:]
['Bob', 'Jack']
>>> L[-2:-1]
['Bob']

# 创建0~99的数列
>>> L = list(range(100))
>>> L
[0, 1, 2, 3, ..., 99]

# 只写[:]就可以原样复制一个list
>>> L[:]
[0, 1, 2, 3, ..., 99]

# tuple也是一种list，唯一区别是tuple不可变。因此，tuple也可以用切片操作，只是操作的结果仍是tuple
>>> (0, 1, 2, 3, 4, 5)[:3]
(0, 1, 2)

# 字符串
>>> 'ABCDEFG'[:3]
'ABC'
>>> 'ABCDEFG'[::2]
'ACEG'
```

```python
 # 利用切片操作，实现一个trim()函数，去除字符串首尾的空格，注意不要调用str的strip()方法：
 while s[:1] == ' ' or s[-1:] == ' ':   
    if s[:1] == ' ':
       s = s[1:]
    if s[-1:] == ' ':
       s = s[:-1]
 return s
```

## 迭代

**Python中`for`循环可以用在所有可迭代对象上**

- dict

  ```python
  >>> d = {'a': 1, 'b': 2, 'c': 3}
  >>> for key in d:
  ...     print(key)
  ...
  a
  c
  b
  
  # 迭代value
  >>> for value in d.values():
  ...     print(value)
  ...
  1
  3
  2
  
  # 同时迭代key和value
  >>> for k, v in d.items():
  ...     print(k, v)
  ...
  a 1
  b 2
  c 3
  ```

**判断对象是否可迭代**

```python
>>> from collections import Iterable
>>> isinstance('abc', Iterable) # str是否可迭代
True
>>> isinstance([1,2,3], Iterable) # list是否可迭代
True
>>> isinstance(123, Iterable) # 整数是否可迭代
False
```

**`enumerate`可以将list变成索引-元素对**

```python
>>> for i, value in enumerate(['A', 'B', 'C']):
...     print(i, value)
...
0 A
1 B
2 C
```

## 列表生成式

```python
>>> [x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

# for后面加上if判断可以用作筛选元素
>>> [x * x for x in range(1, 11) if x % 2 == 0]
[4, 16, 36, 64, 100]

# 两层循环，生成全排列
>>> [m + n for m in 'ABC' for n in 'XYZ']
['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']

# 使用两个变量生成list
>>> d = {'x': 'A', 'y': 'B', 'z': 'C' }
>>> [k + '=' + v for k, v in d.items()]
['y=B', 'x=A', 'z=C']

>>> L = ['Hello', 'World', 'IBM', 'Apple']
>>> [s.lower() for s in L]
['hello', 'world', 'ibm', 'apple']

# for后面的if是一个筛选条件，不能带else
>>> [x for x in range(1, 11) if x % 2 == 0 else 0]
  File "<stdin>", line 1
    [x for x in range(1, 11) if x % 2 == 0 else 0]
                                              ^
SyntaxError: invalid syntax
  
>>> [x if x % 2 == 0 else -x for x in range(1, 11)]
[-1, 2, -3, 4, -5, 6, -7, 8, -9, 10]

# 在一个列表生成式中，for前面的if ... else是表达式，而for后面的if是过滤条件，不能带else
```

## 生成器

- 可用将列表生成式的`[]`改成`()` ，就可以创建一个生成器generator

  ```python
  >>> L = [x * x for x in range(10)]
  >>> L
  [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
  >>> g = (x * x for x in range(10))
  >>> g
  <generator object <genexpr> at 0x1022ef630>
  ```

- 通过`next()`方法获取generator的下一个返回值，当没有下一个返回值时，抛出`StopIteration`的错误

- 使用`for`循环获取

  ```python
  >>> g = (x * x for x in range(10))
  >>> for n in g:
  ...     print(n)
  ... 
  0
  1
  4
  9
  16
  25
  36
  49
  64
  81
  
  ```

- 通过函数实现generator，使用`yield`关键字

  ```python
  def fib(max):
      n, a, b = 0, 0, 1
      while n < max:
          yield b
          a, b = b, a + b
          n = n + 1
      return 'done'
  ```

- generator和函数执行流程的区别，函数是顺序执行，遇到`return`语句或者最后一行函数语句就返回，generator函数在每次调用`next()`的时候执行，遇到`yield`语句返回，再次执行时从上次返回的`yield`语句出继续执行。

- for循环调用generator时，拿不到return的返回值，需要捕获StopIteration错误，从中获取

  ```python
  >>> g = fib(6)
  >>> while True:
  ...     try:
  ...         x = next(g)
  ...         print('g:', x)
  ...     except StopIteration as e:
  ...         print('Generator return value:', e.value)
  ...         break
  ...
  g: 1
  g: 1
  g: 2
  g: 3
  g: 5
  g: 8
  Generator return value: done
  ```

## 迭代器

>凡是可作用于`for`循环的对象都是`Iterable`类型；
>
>凡是可作用于`next()`函数的对象都是`Iterator`类型，它们表示一个惰性计算的序列；
>
>集合数据类型如`list`、`dict`、`str`等是`Iterable`但不是`Iterator`，不过可以通过`iter()`函数获得一个`Iterator`对象。
>
>Python的`for`循环本质上就是通过不断调用`next()`函数实现的，例如：
>
>```python
>for x in [1, 2, 3, 4, 5]:
>pass
># 首先获得Iterator对象:
>it = iter([1, 2, 3, 4, 5])
># 循环:
>while True:
>try:
>   # 获得下一个值:
>   x = next(it)
>except StopIteration:
>   # 遇到StopIteration就退出循环
>   break
>```

## 参考资料

[高级特性](https://www.liaoxuefeng.com/wiki/1016959663602400/1017269809315232)


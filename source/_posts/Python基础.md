---
title: Python基础
date: 2021-01-24 14:25:58
tags: Python
---

![](https://oss.forestyoung.top/field-5430070_1920.jpg)

<!--more-->

## [数据类型和变量](https://www.liaoxuefeng.com/wiki/1016959663602400/1017063826246112)

### 数据类型

- 整数：

  - Python允许在数字中间以`_`分隔，`10_000_000_000`和`10000000000`是完全一样的
  - **Python的整数没有大小限制，而某些语言的整数根据其存储长度是有大小限制的**

- 浮点数

  - 整数和浮点数在计算机内部存储的方式是不同的，整数运算永远是精确的（**除法难道也是精确的？是的！**），而浮点数运算则可能会有四舍五入的误差。

  - Python的浮点数也没有大小限制，但是超出一定范围就直接表示为`inf`（无限大）。

  - 除法为什么也是精确的

    > Python中有两种除法，`/`和`//`（地板除）
    >
    > 1.`/`除法计算结果是浮点数，即使是两个整数恰好整除，结果也是浮点数：
    >
    > ```python
    > >>> 10 / 3
    > 3.3333333333333335
    > >>> 9 / 3
    > 3.0
    > ```
    >
    > 2.`//`两个整数的除法仍然是整数，整数的地板除`//`永远是整数，即使除不尽。要做精确的除法，使用`/`就可以。
    >
    > ```python
    > >>> 10 // 3
    > 3
    > ```
    >
    > 3.`//`除法只取结果的整数部分，所以Python还提供一个余数运算，可以得到两个整数相除的余数：
    >
    > ``` python
    > >>> 10 % 3
    > 1
    > ```
    >
    > 

- 字符串

  - 以单引号`'`或双引号`"`括起来的任意文本，转义字符`\`
  - 如果字符串里面有很多字符都需要转义，就需要加很多`\`，为了简化，Python还允许用`r''`表示`''`内部的字符串默认不转义
  - 如果字符串内部有很多换行，用`\n`写在一行里不好阅读，为了简化，Python允许用`'''...'''`的格式表示多行内容

- 布尔

  - 布尔值只有`True`、`False`两种值，**注意大小写**

  - 布尔值可以用`and`、`or`和`not`运算。

  - ```python
    >>> True and True
    True
    >>> True and False
    False
    >>> False and False
    False
    >>> 5 > 3 and 3 > 1
    True
    >>> True or True
    True
    >>> True or False
    True
    >>> False or False
    False
    >>> 5 > 3 or 1 > 3
    True
    >>> not True
    False
    >>> not False
    True
    >>> not 1 > 2
    True
    ```

- 空值

  - 用`None`表示

### 变量

- 变量不仅可以是数字，还可以是任意数据类型。
- 变量在程序中就是用一个变量名表示了，变量名必须是大小写英文、数字和`_`的组合，且不能用数字开头
- 在Python中，等号`=`是赋值语句，可以把任意数据类型赋值给变量，同一个变量可以反复赋值，而且可以是不同类型的变量，这种变量本身类型不固定的语言称之为**动态语言**，类比Java是静态语言
- 变量在计算机内存中的表示，例：a = 'ABC'，Python解释器干了两件事情：
  1. 在内存中创建了一个`'ABC'`的字符串；
  2. 在内存中创建了一个名为`a`的变量，并把它指向`'ABC'`。

### 常量

- 在Python中，通常用全部大写的变量名表示常量，例：PI = 3.14159265359，ps：但事实上`PI`仍然是一个变量，Python根本没有任何机制保证`PI`不会被改变，所以，用全部大写的变量名表示常量只是一个习惯上的用法，如果你一定要改变变量`PI`的值，也没人能拦住你

## [字符串和编码](https://www.liaoxuefeng.com/wiki/1016959663602400/1017075323632896)

### 字符编码

> 因为计算机只能处理数字，如果要处理文本，就必须先把文本转换为数字才能处理。最早的计算机在设计时采用8个比特（bit）作为一个字节（byte），所以，一个字节能表示的最大的整数就是255（二进制11111111=十进制255），如果要表示更大的整数，就必须用更多的字节。比如两个字节可以表示的最大整数是`65535`，4个字节可以表示的最大整数是`4294967295`。
>
> 由于计算机是美国人发明的，因此，最早只有127个字符被编码到计算机里，也就是大小写英文字母、数字和一些符号，这个编码表被称为`ASCII`编码，比如大写字母`A`的编码是`65`，小写字母`z`的编码是`122`。
>
> 但是要处理中文显然一个字节是不够的，至少需要两个字节，而且还不能和ASCII编码冲突，所以，中国制定了`GB2312`编码，用来把中文编进去。
>
> 你可以想得到的是，全世界有上百种语言，日本把日文编到`Shift_JIS`里，韩国把韩文编到`Euc-kr`里，各国有各国的标准，就会不可避免地出现冲突，结果就是，在多语言混合的文本中，显示出来会有乱码。
>
> 因此，Unicode字符集应运而生。Unicode把所有语言都统一到一套编码里，这样就不会再有乱码问题了。
>
> Unicode标准也在不断发展，但最常用的是UCS-16编码，用两个字节表示一个字符（如果要用到非常偏僻的字符，就需要4个字节）。现代操作系统和大多数编程语言都直接支持Unicode。
>
> 现在，捋一捋ASCII编码和Unicode编码的区别：ASCII编码是1个字节，而Unicode编码通常是2个字节。
>
> 字母`A`用ASCII编码是十进制的`65`，二进制的`01000001`；
>
> 字符`0`用ASCII编码是十进制的`48`，二进制的`00110000`，注意字符`'0'`和整数`0`是不同的；
>
> 汉字`中`已经超出了ASCII编码的范围，用Unicode编码是十进制的`20013`，二进制的`01001110 00101101`。
>
> 你可以猜测，如果把ASCII编码的`A`用Unicode编码，只需要在前面补0就可以，因此，`A`的Unicode编码是`00000000 01000001`。
>
> 新的问题又出现了：如果统一成Unicode编码，乱码问题从此消失了。但是，如果你写的文本基本上全部是英文的话，用Unicode编码比ASCII编码需要多一倍的存储空间，在存储和传输上就十分不划算。
>
> 所以，本着节约的精神，又出现了把Unicode编码转化为“可变长编码”的`UTF-8`编码。UTF-8编码把一个Unicode字符根据不同的数字大小编码成1-6个字节，常用的英文字母被编码成1个字节，汉字通常是3个字节，只有很生僻的字符才会被编码成4-6个字节。如果你要传输的文本包含大量英文字符，用UTF-8编码就能节省空间：
>
> | 字符 | ASCII    | Unicode           | UTF-8                      |
> | :--- | :------- | :---------------- | :------------------------- |
> | A    | 01000001 | 00000000 01000001 | 01000001                   |
> | 中   | x        | 01001110 00101101 | 11100100 10111000 10101101 |
>
> 从上面的表格还可以发现，UTF-8编码有一个额外的好处，就是ASCII编码实际上可以被看成是UTF-8编码的一部分，所以，大量只支持ASCII编码的历史遗留软件可以在UTF-8编码下继续工作。
>
> 搞清楚了ASCII、Unicode和UTF-8的关系，我们就可以总结一下现在计算机系统通用的字符编码工作方式：
>
> 在计算机内存中，统一使用Unicode编码，当需要保存到硬盘或者需要传输的时候，就转换为UTF-8编码。

### Python的字符串

- 在Python 3版本中，字符串以Unicode编码。

- `ord()`函数获取字符的整数表示，`chr()`函数把编码转换为对应的字符。

  ```python
  >>> ord('A')
  65
  >>> ord('中')
  20013
  >>> chr(66)
  'B'
  >>> chr(25991)
  '文'
  ```

- `bytes`类型的数据用带`b`前缀的单引号或双引号，例：x = b'ABC'

- `encode()`和`decode()`

  ```python
  >>> 'ABC'.encode('ascii')
  b'ABC'
  >>> '中文'.encode('utf-8')
  b'\xe4\xb8\xad\xe6\x96\x87'
  >>> '中文'.encode('ascii')
  Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
  UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1: ordinal not in range(128)
  >>> b'ABC'.decode('ascii')
  'ABC'
  >>> b'\xe4\xb8\xad\xe6\x96\x87'.decode('utf-8')
  '中文'
  >>> b'\xe4\xb8\xad\xff'.decode('utf-8')
  Traceback (most recent call last):
    ...
  UnicodeDecodeError: 'utf-8' codec can't decode byte 0xff in position 3: invalid start byte
    >>> b'\xe4\xb8\xad\xff'.decode('utf-8', errors='ignore')
  '中'
  ```

- 计算字符串包含多少个字符，用`len()`函数，如果是bytes，计算的是字节数

  ```python
  >>> len('ABC')
  3
  >>> len('中文')
  2
  ```

- **在操作字符串时，我们经常遇到`str`和`bytes`的互相转换。为了避免乱码问题，应当始终坚持使用UTF-8编码对`str`和`bytes`进行转换。**

### 格式化

- 在Python中，采用的格式化方式和C语言是一致的，用`%`实现

> 常见的占位符有：
>
> | 占位符 | 替换内容     |
> | :----- | :----------- |
> | %d     | 整数         |
> | %f     | 浮点数       |
> | %s     | 字符串       |
> | %x     | 十六进制整数 |

- ### format()

- ### f-string

### [list和tuple](https://www.liaoxuefeng.com/wiki/1016959663602400/1017092876846880)

> **list和tuple是Python内置的有序集合，一个可变，一个不可变**

#### list

- len()获取元素个数
- 索引从0开始，超出范围会报`IndexError`错误，可通过list[-1]获取最后一个元素，以此类推，同样需要注意越界问题
- append()追加元素到末尾
- insert(1, "")可以插入元素到指定位置
- pop()删除list末尾的元素，pop(i)删除指定位置的元素
- list中的元素数据类型可以不同，类似Java中的Object List
- list元素也可以是另一个list，例：s = ['python', 'java', ['asp', 'php'], 'scheme']，'php'`可以写`p[1]`或者`s[2] [1]，可以看成二维数组

#### tuple

- 初始化后不能修改，无append()、insert()方法，获取元素的方法与list一样

- 不可变的tuple有什么意义？因为tuple不可变，所以代码更安全。如果可能，能用tuple代替list就尽量用tuple。

- 定义一个tuple时，tuple的元素必须确定下来，注意：只有一个元素时必须加上逗号与数学公式中的小括号区分

  ```python
  >>> t = (1, 2)
  >>> t
  (1, 2)
  # 空的tuple
  >>> t = ()
  >>> t
  ()
  # 这里定义的不是tuple，表示的是1这个数
  >>> t = (1)
  >>> t
  1
  # 正确的定义方式
  >>> t = (1,)
  >>> t
  (1,)
  ```

- tuple的不变指的是“指向不变”，例：tuple中包含list，list的指向是可变的

## [条件判断](https://www.liaoxuefeng.com/wiki/1016959663602400/1017099478626848)

```python
age = 20
if age >= 18:
    print('your age is', age)
    print('adult')
    
age = 3
if age >= 18:
    print('your age is', age)
    print('adult')
else:
    print('your age is', age)
    print('teenager')
    
age = 3
if age >= 18:
    print('adult')
elif age >= 6:
    print('teenager')
else:
    print('kid')
```

### [循环](https://www.liaoxuefeng.com/wiki/1016959663602400/1017100774566304)

#### 两种循环

- for...in

  ```python
  sum = 0
  for x in [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]:
      sum = sum + x
  print(sum)
  
  sum = 0
  for x in range(101):
      sum = sum + x
  print(sum)
  range()函数，可以生成一个整数序列，再通过list()函数可以转换为list。比如range(5)生成的序列是从0开始小于5的整数
  ```

- while

  ```python
  sum = 0
  n = 99
  while n > 0:
      sum = sum + n
      n = n - 2
  print(sum)
  ```

- 使用enumerate()函数可以获得元素下标

  ```python
  for inx, val in enumerate(['uyy', 'dfdf']):
  	    print(inx)
  	    print(val)
  ```

#### break和continue

- break退出当前循环
- continue跳过本次循环，进入下次循环

#### [使用dict和set](https://www.liaoxuefeng.com/wiki/1016959663602400/1017104324028448)

#### 不可变对象

- 不可变对象，如字符串、整数，可变对象，如list

- a是变量，'abc'是字符串对象

  ```python
  >>> a = 'abc'
  >>> a.replace('a', 'A')
  'Abc'
  >>> a
  'abc'
  ```

- 对不可变对象调用自身的任意方法，不会改变对象本身，只是这些方法会创建新的对象并返回，这样保证了不可变对象本身永远是不可变的

#### dict

```python
>>> d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
>>> d['Michael']
95

>>> d['Adam'] = 67
>>> d['Adam']
67

>>> d['Jack'] = 90
>>> d['Jack']
90
>>> d['Jack'] = 88
>>> d['Jack']
88

>>> d['Thomas']
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'Thomas'
```

- **dict的key必须是不可变对象**，dict是根据key计算value的存储位置，如果相同的key计算出不同的结果，dict内部就完全混乱了

- dict全称dictionary，在其他语言中也称为map，使用键-值（key-value）存储，具有极快的查找速度。

- `in`可判断key是否存在

  ```python
  >>> 'Thomas' in d
  False
  ```

- `get()`方法，如果key不存在，可以返回`None`，或者自己指定的value

  ```python
  >>> d.get('Thomas')
  >>> d.get('Thomas', -1)
  -1
  ```

- `pop(key)`删除对应的key和value

  ```python
  >>> d.pop('Bob')
  75
  >>> d
  {'Michael': 95, 'Tracy': 85}
  ```

#### set

- set和dict的原理一样，区别在于set没有存储对应的value

- 要创建一个set，需要提供一个list作为输入集合：

  ```python
  >>> s = set([1, 2, 3])
  >>> s
  {1, 2, 3}
  ```

- set中key不能重复，重复元素在set中自动被过滤

- `add(key)`方法将元素添加到set中

  ```python
  >>> s.add(4)
  >>> s
  {1, 2, 3, 4}
  >>> s.add(4)
  >>> s
  {1, 2, 3, 4}
  ```

- `remove(key)`方法可以删除元素

  ```python
  >>> s.remove(4)
  >>> s
  {1, 2, 3}
  ```

- set可以看成数学意义上的无序和无重复元素的集合，因此，两个set可以做数学意义上的交集、并集等操作

  ```python
  >>> s1 = set([1, 2, 3])
  >>> s2 = set([2, 3, 4])
  >>> s1 & s2
  {2, 3}
  >>> s1 | s2
  {1, 2, 3, 4}
  ```

#### tuple放入dict和set中

**tuple虽然是不变对象，但试试把`(1, 2, 3)`和`(1, [2, 3])`放入dict或set中，并解释结果。**

```python
#tuple放入dict：
>>> t=(1,2,3)
>>> d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
>>> d[t]=63
>>> d
{'Michael': 95, 'Bob': 75, 'Tracy': 85, (1, 2, 3): 63}
>>> d[1]=t
>>> d
{'Michael': 95, 'Bob': 75, 'Tracy': 85, (1, 2, 3): 63, 1: (1, 2, 3)}
>>> t=(1,[2,3])
>>> d[t]=32
Traceback (most recent call last):  File "<stdin>", line 1, in <module> TypeError: unhashable type: 'list'
>>> d[2]=t
>>> d
{'Michael': 95, 'Bob': 75, 'Tracy': 85, (1, 2, 3): 63, 1: (1, 2, 3), 2: (1, [2, 3])}

#tuple放入set：
>>> s
{2, 3, 4}
>>> t
(1, [2, 3])
>>> s.add(t)
Traceback (most recent call last):  File "<stdin>", line 1, in <module> TypeError: unhashable type: 'list'
>>> t=(1,2,3)
>>> s.add(t)
>>> s
{2, 3, 4, (1, 2, 3)}
```

> 1. 分别把“可变”tuple（含list的tuple）和不可变的tuple放入dict的key和value：
>
>    “可变tuple”放入dict的key显示非法hash的key；不可变tapule正常放入。
>
> 2. 把“可变tuple”（含list的tuple）和不可变tuple放入set：
>
>    “可变tuple”不可放入set（无法判断是否和已有元素相同）；不可变tuple放入set正常。

>要理解dict的有关内容需要你理解哈希表（map）的相关基础知识，这个其实是《算法与数据结构》里面的内容。
>
>1.list和tuple其实是用链表顺序存储的，也就是前一个元素中存储了下一个元素的位置，这样只要找到第一个元素的位置就可以顺藤摸瓜找到所有元素的位置，所以list的名字其实就是个指针，指向list的第一个元素的位置。list的插入和删除等可以直接用链表的方式进行，比如我要在第1个元素和第2个元素中间插入一个元素，那么直接在链表的最后面（我们假设这个list只有两个元素，那么也就是在第3个元素的位置上）插入这个元素，然后把第一个元素指针指向这个元素（第3个位置），然后再把新插入的元素的指针指向原来的第2个元素，这样插入操作就完成了。读取这个list的时候，先用list的名字（就是个指针，指向第1个元素的位置）找到第一个元素，然后用第1一个元素的指针找到第2个元素（位置3），然后用第2个元素的指针找到第3个元素（位置2），以此类推。所以list的顺序和内存中的实际顺序其实不一定完全对应。这种存储方式不会浪费内存，但查找起来特别费时间，因为要按照链表一个一个找下去，如果你的list特别大的话，那么要等好久才会找到结果。
>
>2.dict则为了快速查找使用了一种特别的方法，哈希表。哈希表采用哈希函数从key计算得到一个数字（哈希函数有个特点：对于不同的key，有很大的概率得到的哈希值也不同），然后直接把value存储到这个数字所对应的地址上，比如key='ABC'，value=10，经过哈希函数得到key对应的哈希值为123，那么就申请一个有1000个地址（从0到999）的内存，然后把10存放在地址为123的地方。类似的，对于key='BCD'，value=20，得到key的哈希值为234，那么就把20存放在地址为234的地方。对于这样的表查找起来是非常方便的。只要给出key，计算得到哈希值，然后直接到对应的地址去找value就可以了。无论有几个元素，都可以直接找到value，无需遍历整个表。不过虽然dict查找速度快，但内存浪费严重，你看我们只存储了两个元素，都要申请一个长度为1000的内存。
>
>3.现在你知道为啥key要用不可变对象了吧？因为不可变对象是常量，每次的哈希值算出来都是固定的，这样就不会出错。比如key='ABC'，value=10，存储地址为123，假设我突发奇想，把key改成'BCD'，那么当查找'BCD'的value的时候就会去234的地址找，但那里啥也没有，这就乱套了。
>
>4.你看我们上面有一句话：对于不同的key，有很大的概率得到的哈希值也不同。那么有很小的概率不同的key可以得到相同的哈希值了？没错，比如对于我们的例子来说，哈希值只有3位，那么只要元素个数超过1000，就一定会有至少两个key的哈希值相同（鸽笼原理），这种情况叫“冲突”，设计哈希表的时候要采取办法减少冲突，实在冲突了也要想办法补救。不过这是编译器的事情，况且对于初学者的我们来说碰到的冲突的概率基本等于零，就不用操心了。

## 参考资料

[Python基础](https://www.liaoxuefeng.com/wiki/1016959663602400/1017063413904832)


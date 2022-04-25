## 第2章 Python 语言



### 关于 Python

Python 是一种通用的高级编程语言。它的设计理念强调程序员的生产力和代码的可读性。它具有极简的核心语法，基本命令很少，语义也很简单，但它也有一个庞大而全面的标准库，包括一个应用程序编程接口 (API)

到许多底层操作系统 (OS) 功能。Python 代码虽然极简，但定义了内置对象，例如链表 ( `list`)、元组 ( `tuple`)、哈希表 ( `dict`) 和任意长整数 ( `long`)。



Python 支持多种编程范式，包括面向对象 ( `class`)、命令式 ( `def`) 和函数式 ( `lambda`) 编程。Python 有一个动态类型系统和使用引用计数的自动内存管理（类似于 Perl、Ruby 和 Scheme）。

Python 由 Guido van Rossum 于 1991 年首次发布。该语言具有开放的、基于社区的开发模型，由非营利性 Python 软件基金会管理。有许多实现 Python 语言的解释器和编译器，包括 Java (Jython) 中的一个，但在这个简短的回顾中，我们参考了 Guido 创建的参考 C 实现。

您可以在 Python 官方网站上找到该语言的许多教程、官方文档和库参考。[[python](http://web2py.com/books/default/reference/29/python)]

对于其他 Python 参考资料，我们可以推荐参考文献中的书籍。(http://web2py.com/books/default/reference/29/guido) ] 和参考。(http://web2py.com/books/default/reference/29/lutz)]。

如果您已经熟悉 Python 语言，则可以跳过本章。



### 启动

可以在线的python运行。注册pythonanywhere.com。打开consoles，运行以下代码。print语句要加（）。



### 帮助，目录





Python 语言提供了两个命令来获取有关在当前范围内定义的对象的文档，包括内置的和用户定义的。

我们可以询问`help`一个对象，例如“1”：

```
>>> help(1)
Help on int object:

class int(object)
 |  int(x=0) -> int or long
 |  int(x, base=10) -> int or long
 |  
 |  Convert a number or string to an integer, or return 0 if no arguments
 |  are given.  If x is floating point, the conversion truncates towards zero.
 |  If x is outside the integer range, the function returns a long instead.
 |  
 |  If x is not a number or if base is given, then x must be a string or
 |  Unicode object representing an integer literal in the given base.  The
 |  literal can be preceded by '+' or '-' and be surrounded by whitespace.
 |  The base defaults to 10.  Valid bases are 0 and 2-36.  Base 0 means to
 |  interpret the base from the string as an integer literal.
 |  >>> int('0b100', base=0)
 |  4
 |  
 |  Methods defined here:
 |
 |  __abs__(...)
 |      x.__abs__() <==> abs(x)
...
```

并且，由于“1”是一个整数，我们得到了关于`int`类及其所有方法的描述。此处的输出已被截断，因为它非常长且详细。

同样，我们可以通过以下命令获取对象“1”的方法列表`dir`：

```
>>> dir ( 1 ) 
['__abs__', '__add__', '__and__', ...
```



### 类型

Python 是一种动态类型语言，这意味着变量没有类型，因此不必声明。另一方面，值确实有类型。您可以查询变量以了解其包含的值的类型：

```
>>> a = 3
>>> print type(a)
<type 'int'>
>>> a = 3.14
>>> print type(a)
<type 'float'>
>>> a = 'hello python'
>>> print type(a)
<type 'str'>
```

Python 本身还包括数据结构，例如列表和字典。



#### `str`





Python 支持使用两种不同类型的字符串：ASCII 字符串和 Unicode 字符串。ASCII 字符串由 '...'、"..." 或*'..* ' 或 """...""" 分隔。三引号分隔多行字符串。Unicode 字符串以 a 开头，`u`后跟包含 Unicode 字符的字符串。可以通过选择编码将 Unicode 字符串转换为 ASCII 字符串，例如：

```
>>> a = 'this is an ASCII string'
>>> b = u'This is a Unicode string'
>>> a = b.encode('utf8')
```

执行这三个命令后，结果`a`是一个存储 UTF8 编码字符的 ASCII 字符串。按照设计，web2py 在内部使用 UTF8 编码的字符串。

也可以通过各种方式将变量写入字符串：

```
>>> print 'number is ' + str(3)
number is 3
>>> print 'number is %s' % (3)
number is 3
>>> print 'number is %(number)s' % dict(number=3)
number is 3
```

最后一种表示法更明确且不易出错，因此是首选。

许多 Python 对象，例如数字，可以使用`str`或序列化为字符串`repr`。这两个命令非常相似，但产生的输出略有不同。例如：

```
>>> for i in [3, 'hello']:
...     print str(i), repr(i)
... 
3 3
hello 'hello'
```

对于用户定义的类，`str`可以`repr`使用特殊运算符`__str__`和`__repr__`. 稍后将简要介绍这些内容；有关更多信息，请参阅官方 Python 文档[ [pydocs](http://web2py.com/books/default/reference/29/pydocs) ]。`repr`总是有一个默认值。

Python 字符串的另一个重要特征是，与列表一样，它是一个可迭代对象。

```
>>> for  i  in  'hello' : 
...     print  i 
... 
h 
e 
l 
l 
o
```



#### `list`

Python 列表的主要方法是追加、插入和删除：

```
>>> a = [1, 2, 3]
>>> print type(a)
<type 'list'>
>>> a.append(8)
>>> a.insert(2, 7)
>>> del a[0]
>>> print a
[2, 7, 3, 8]
>>> print len(a)
4
```

列表可以切片：

```
>>> print a[:3]
[2, 7, 3]
>>> print a[1:]
[7, 3, 8]
>>> print a[-2:]
[3, 8]
```

并串联：

```
>>> a = [2, 3]
>>> b = [5, 6]
>>> print a + b
[2, 3, 5, 6]
```

列表是可迭代的；你可以循环它：

```
>>> a = [1, 2, 3]
>>> for i in a:
...     print i
... 
1
2
3
```

列表的元素不必是同一类型；它们可以是任何类型的 Python 对象。

有一种非常常见的情况可以使用*列表推导式。*考虑以下代码：

```
>>> a = [1, 2, 3, 4, 5]
>>> b = []
>>> for x in a:
...     if x % 2 == 0:
...         b.append(x * 3)
... 
>>> b
[6, 12]
```

这段代码清楚地处理了一个项目列表，选择并修改了输入列表的一个子集，并创建了一个新的结果列表，这段代码可以完全替换为以下列表推导：

```
>>> a = [1, 2, 3, 4, 5]
>>> b = [x * 3 for x in a if x % 2 == 0]
>>> b
[6, 12]
```



#### `tuple`

元组就像一个列表，但它的大小和元素是不可变的，而在列表中它们是可变的。如果元组元素是对象，则对象属性是可变的。元组由圆括号分隔。

```
>>> a = (1, 2, 3)
```

因此，虽然这适用于列表：

```
>>> a = [1, 2, 3]
>>> a[1] = 5
>>> print a
[1, 5, 3]
```

元素分配不适用于元组：

```
>>> a = (1, 2, 3)
>>> print a[1]
2
>>> a[1] = 5
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
```

一个元组，就像一个列表，是一个可迭代的对象。请注意，由单个元素组成的元组必须包含尾随逗号，如下所示：

```
>>> a = (1)
>>> print type(a)
<type 'int'>
>>> a = (1, )
>>> print type(a)
<type 'tuple'>
```

由于元组的不变性，元组对于有效打包对象非常有用，并且括号通常是可选的：

```
>>> a = 2, 3, 'hello'
>>> x, y, z = a
>>> print x
2
>>> print z
hello
```



#### `dict`

Python `dict`-ionary 是一个将键对象映射到值对象的哈希表。例如：

```
>>> a = {'k':'v', 'k2':3}
>>> a['k']
v
>>> a['k2']
3
>>> a.has_key('k')
True
>>> a.has_key('v')
False
```

键可以是任何可散列类型（int、string 或任何其类实现该`__hash__`方法的对象）。值可以是任何类型。同一个字典中的不同键和值不必是相同的类型。如果键是字母数字字符，也可以使用替代语法声明字典：

```
>>> a = dict(k='v', h2=3)
>>> a['k']
v
>>> print a
{'k':'v', 'h2':3}
```

有用的方法是`has_key`、`keys`和：`values``items`

```
>>> a = dict(k='v', k2=3)
>>> print a.keys()
['k', 'k2']
>>> print a.values()
['v', 3]
>>> print a.items()
[('k', 'v'), ('k2', 3)]
```

该`items`方法生成一个元组列表，每个元组都包含一个键及其关联的值。

可以使用以下命令删除字典元素和列表元素`del`：

```
>>> a = [1, 2, 3]
>>> del a[1]
>>> print a
[1, 3]
>>> a = dict(k='v', h2=3)
>>> del a['h2']
>>> print a
{'k':'v'}
```

在内部，Python 使用`hash`运算符将对象转换为整数，并使用该整数来确定存储值的位置。

```
>>> hash("hello world")
-1500746465
```



### 关于缩进

Python 使用缩进来分隔代码块。一个块以冒号结尾的行开始，并持续到与下一行具有相似或更高缩进的所有行。例如：

```
>>> i = 0
>>> while i < 3:
...     print i
...     i = i + 1
... 
0
1
2
```

每个缩进级别通常使用四个空格。最好不要将制表符与空格混合，这会导致（不可见的）混乱。



### `for...in`

在 Python 中，您可以循环遍历可迭代对象：

```
>>> a = [0, 1, 'hello', 'python']
>>> for i in a:
...     print i
... 
0
1
hello
python
```

一个常见的快捷方式是`xrange`，它生成一个可迭代的范围而不存储整个元素列表。

```
>>> for i in xrange(0, 4):
...     print i
... 
0
1
2
3
```

这等效于 C/C++/C#/Java 语法：

```
for(int i=0; i<4; i=i+1) { print(i); }
```

另一个有用的命令是`enumerate`，它在循环时计数：

```
>>> a = [0, 1, 'hello', 'python']
>>> for i, j in enumerate(a):
...     print i, j
... 
0 0
1 1
2 hello
3 python
```

还有一个关键字`range(a, b, c)`返回一个整数列表，从 value 开始`a`，递增`c`，最后一个小于 的值结束`b`， `a`默认为 0，`c`默认为 1。`xrange`类似但实际上并不生成列表，只是一个迭代器名单; 因此它更适合循环。

您可以使用跳出循环`break`

```
>>> for i in [1, 2, 3]:
...     print i
...     break
... 
1
```

您可以跳转到下一个循环迭代，而无需执行整个代码块`continue`

```
>>> for i in [1, 2, 3]:
...     print i
...     continue
...     print 'test'
... 
1
2
3
```



### `while`

Python 中的`while`循环与在许多其他编程语言中的工作方式一样，通过无限次循环并在每次迭代之前测试一个条件。如果条件为`False`，则循环结束。

```
>>> i = 0
>>> while i < 10:
...     i = i + 1
... 
>>> print i
10
```

Python中没有`loop...until`构造。



### `if...elif...else`



Python 中条件的使用很直观：



```
>>> for i in range(3):
...     if i == 0:
...         print 'zero'
...     elif i == 1:
...         print 'one'
...     else:
...         print 'other'
... 
zero
one
other
```

“elif”的意思是“否则”。`elif`和`else`子句都是可选的。可以有多个`elif`但只有一个`else`语句。可以使用`not`,`and`和`or`运算符创建复杂条件。

```
>>> for i in range(3):
...     if i == 0 or (i == 1 and i + 1 == 2):
...         print '0 or 1'
... 
0 or 1
0 or 1
```



### `try...except...else...finally`



Python 可以抛出 - 赦免、引发 - 异常：



```
>>> try:
...     a = 1 / 0
... except Exception as e:
...     print 'oops: %s' % e
... else:
...     print 'no problem here'
... finally:
...     print 'done'
... 
oops: integer division or modulo by zero
done
```

如果引发异常，它会被`except`执行的子句捕获，而`else`子句不被执行。如果没有引发异常，`except`则不会执行该子句，但会执行该子句`else`。该`finally`子句始终被执行。

对于不同的可能异常，可以有多个`except`子句：

```
>>> try:
...     raise TypeError()
... except ValueError:
...     print 'value error'
... except Exception:
...     print 'generic error'
... 
generic error
```

`else`and`finally`子句是可选的。

这是内置 Python 异常 + HTTP 的列表（由 web2py 定义）

```
BaseException
 +-- SystemExit
 +-- KeyboardInterrupt
 +-- GeneratorExit
 +-- Exception
      +-- HTTP (defined by web2py)
      +-- StopIteration
      +-- StandardError
      |    +-- BufferError
      |    +-- ArithmeticError
      |    |    +-- FloatingPointError
      |    |    +-- OverflowError
      |    |    +-- ZeroDivisionError
      |    +-- AssertionError
      |    +-- AttributeError
      |    +-- EnvironmentError
      |    |    +-- IOError
      |    |    +-- OSError
      |    |         +-- WindowsError (Windows)
      |    |         +-- VMSError (VMS)
      |    +-- EOFError
      |    +-- ImportError
      |    +-- LookupError
      |    |    +-- IndexError
      |    |    +-- KeyError
      |    +-- MemoryError
      |    +-- NameError
      |    |    +-- UnboundLocalError
      |    +-- ReferenceError
      |    +-- RuntimeError
      |    |    +-- NotImplementedError
      |    +-- SyntaxError
      |    |    +-- IndentationError
      |    |         +-- TabError
      |    +-- SystemError
      |    +-- TypeError
      |    +-- ValueError
      |         +-- UnicodeError
      |              +-- UnicodeDecodeError
      |              +-- UnicodeEncodeError
      |              +-- UnicodeTranslateError
      +-- Warning
           +-- DeprecationWarning
           +-- PendingDeprecationWarning
           +-- RuntimeWarning
           +-- SyntaxWarning
           +-- UserWarning
           +-- FutureWarning
           +-- ImportWarning
           +-- UnicodeWarning
           +-- BytesWarning
```

有关它们中的每一个的详细描述，请参阅官方 Python 文档。

web2py 只公开了一个新异常，称为`HTTP`. 引发时，它会导致程序返回一个 HTTP 错误页面（更多信息请参阅第 4 章）。

任何对象都可以作为异常引发，但最好引发扩展内置异常类之一的对象。



### `def...return`





函数使用`def`. 这是一个典型的 Python 函数：

```
>>> def f(a, b):
...     return a + b
... 
>>> print f(4, 2)
6
```

没有必要（或方式）指定参数的类型或返回类型。在此示例中，`f`定义了一个可以接受两个参数的函数。

函数是本章描述的第一个代码语法特性，用于介绍*作用域*或*命名空间*的概念。在上面的示例中，标识符`a`和`b`在 function 范围之外未定义`f`：

```
>>> def f(a):
...     return a + 1
... 
>>> print f(1)
2
>>> print a
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'a' is not defined
```

在函数范围之外定义的标识符可以在函数内访问；观察标识符`a`在以下代码中是如何处理的：

```
>>> a = 1
>>> def f(b):
...     return a + b
... 
>>> print f(1)
2
>>> a = 2
>>> print f(1) # new value of a is used
3
>>> a = 1 # reset a
>>> def g(b):
...     a = 2 # creates a new local a
...     return a + b
... 
>>> print g(2)
4
>>> print a # global a is unchanged
1
```

如果`a`被修改，后续函数调用将使用全局的新值，`a`因为函数定义绑定了标识符的存储位置`a`，而不是`a`函数声明时本身的值；但是，如果`a`在 function 内部赋值`g`，则全局`a`不受影响，因为新的局部`a`隐藏了全局值。外部作用域引用可用于创建*闭包*：

```
>>> def f(x):
...     def g(y):
...         return x * y
...     return g
... 
>>> doubler = f(2) # doubler is a new function
>>> tripler = f(3) # tripler is a new function
>>> quadrupler = f(4) # quadrupler is a new function
>>> print doubler(5)
10
>>> print tripler(5)
15
>>> print quadrupler(5)
20
```

函数`f`创建新函数；请注意，名称的范围`g`完全是`f`. 闭包非常强大。

函数参数可以有默认值，并且可以返回多个结果：

```
>>> def f(a, b=2):
...     return a + b, a - b
... 
>>> x, y = f(5)
>>> print x
7
>>> print y
3
```

函数参数可以通过名称显式传递，这意味着调用者中指定的参数顺序可以不同于定义函数的参数顺序：

```
>>> def f(a, b=2):
...     return a + b, a - b
... 
>>> x, y = f(b=5, a=2)
>>> print x
7
>>> print y
-3
```

函数还可以采用运行时可变数量的参数：

```
>>> def f(*a, **b):
...     return a, b
... 
>>> x, y = f(3, 'hello', c=4, test='world')
>>> print x
(3, 'hello')
>>> print y
{'c':4, 'test':'world'}
```

这里不通过名称 (3, 'hello') 传递的参数存储在 tuple`a`中，而通过名称 ( `c`and `test`) 传递的参数存储在字典中`b`。

在相反的情况下，可以通过解包将列表或元组传递给需要单个位置参数的函数：

```
>>> def f(a, b):
...     return a + b
... 
>>> c = (1, 2)
>>> print f(*c)
3
```

并且可以解包字典以传递关键字参数：

```
>>> def f(a, b):
...     return a + b
... 
>>> c = {'a':1, 'b':2}
>>> print f(**c)
3
```



#### `lambda`

`lambda`提供了一种非常容易地创建一个非常短的未命名函数的方法：

```
>>> a = lambda b: b + 2
>>> print a(3)
5
```

表达式“ `lambda`[a]:[b]”字面意思是“一个带有参数 [a] 并返回 [b] 的函数”。该`lambda`表达式本身未命名，但该函数通过分配给 identifier 来获取名称`a`。的作用域规则同样`def`适用`lambda`，实际上上面的代码，关于`a`，与使用 的函数声明相同`def`：

```
>>> def a(b):
...     return b + 2
... 
>>> print a(3)
5
```

唯一的好处`lambda`是简洁；但是，在某些情况下，简洁可能非常方便。考虑一个调用的函数`map`，它将函数应用于列表中的所有项目，创建一个新列表：

```
>>> a = [1, 7, 2, 5, 4, 8]
>>> map(lambda x: x + 2, a)
[3, 9, 4, 7, 6, 10]
```

使用此代码的大小会增加一倍，`def`而不是`lambda`. 的主要缺点`lambda`是（在 Python 实现中）语法只允许单个表达式；但是，对于更长的函数，`def`可以使用，并且提供函数名称的额外成本会随着函数长度的增加而减少。就像`def`,`lambda`可用于对函数进行*柯里*化：可以通过包装现有函数来创建新函数，以便新函数携带一组不同的参数：

```
>>> def f(a, b): return a + b
>>> g = lambda a: f(a, 3)
>>> g(2)
5
```

柯里化在很多情况下很有用，但其中一种在 web2py 中直接有用：缓存。假设你有一个昂贵的函数来检查它的参数是否是素数：

```
>>> def isprime(number):
...     for p in range(2, number):
...         if (number % p) == 0:
...             return False
...     return True
```

这个功能显然很耗时。

假设你有一个缓存函数`cache.ram`，它接受三个参数：一个键、一个函数和一个秒数。

```
>>> value = cache.ram('key', f, 60)
```

第一次调用时，它调用 function `f()`，将输出存储在内存中的字典中（假设是“d”），然后返回它，使得值是：

```
>>> value = d['key'] = f()
```

第二次调用时，如果键在字典中并且不超过指定的秒数（60），则返回相应的值而不执行函数调用。

```
>>> value = d['key']
```

对于任何输入，您将如何缓存函数**isprime**的输出？方法如下：

```
>>> number = 7
>>> seconds = 60
>>> print cache.ram(str(number), lambda: isprime(number), seconds)
True
>>> print cache.ram(str(number), lambda: isprime(number), seconds)
True
```

输出总是一样的，但是第一次`cache.ram`被调用，`isprime`被调用；第二次不是。

> Python 函数，使用其中一个创建`def`或`lambda`允许根据一组不同的参数重构现有函数。`cache.ram`并且`cache.disk`是 web2py 缓存功能。



### `class`

因为 Python 是动态类型的，所以 Python 类和对象可能看起来很奇怪。其实在声明一个类的时候不需要定义成员变量（属性），同一个类的不同实例可以有不同的属性。属性通常与实例相关联，而不是类（除非声明为“类属性”，这与 C++/Java 中的“静态成员变量”相同）。

这是一个例子：

```
>>> class MyClass(object): pass
>>> myinstance = MyClass()
>>> myinstance.myvariable = 3
>>> print myinstance.myvariable
3
```

请注意，这`pass`是一个什么都不做的命令。在这种情况下，它用于定义一个`MyClass`不包含任何内容的类。`MyClass()`调用类的构造函数（在本例中为默认构造函数）并返回一个对象，即类的一个实例。类定义中的`(object)`表示我们的类扩展了内置`object`类。这不是必需的，但这是一种很好的做法。

这是一个更复杂的类：

```
>>> class MyClass(object):
...     z = 2
...     def __init__(self, a, b):
...         self.x = a
...         self.y = b
...     def add(self):
...         return self.x + self.y + self.z
... 
>>> myinstance = MyClass(3, 4)
>>> print myinstance.add()
9
```

在类中声明的函数是方法。某些方法具有特殊的保留名称。例如，`__init__`是构造函数。除在方法外声明的变量外，所有变量都是方法的局部变量。例如，`z`是一个*类变量*，相当于一个 C++*静态成员变量*，它为类的所有实例保存相同的值。

请注意，它`__init__`接受 3 个参数并`add`接受 1 个参数，但我们分别用 2 个和 0 个参数调用它们。按照惯例，第一个参数表示方法内部用来引用当前对象的本地名称。这里我们`self`用来指代当前对象，但我们也可以使用任何其他名称。与C++ 或Java`self`中的作用相同，但不是保留关键字。`*this``this``self`

在声明嵌套类时，这种语法对于避免歧义是必要的，例如对于另一个类中的方法而言是本地的类。



### 特殊属性、方法和运算符

以双下划线开头的类属性、方法和运算符通常是私有的（即在内部使用但不暴露在类外部），尽管这是解释器不强制执行的约定。

其中一些是保留关键字，具有特殊含义。

例如，这里是其中的三个：

- `__len__`
- `__getitem__`
- `__setitem__`

例如，它们可以用来创建一个像列表一样的容器对象：

```
>>> class MyList(object):
...     def __init__(self, *a): self.a = list(a)
...     def __len__(self): return len(self.a)
...     def __getitem__(self, i): return self.a[i]
...     def __setitem__(self, i, j): self.a[i] = j
... 
>>> b = MyList(3, 4, 5)
>>> print b[1]
4
>>> b.a[1] = 7
>>> print b.a
[3, 7, 5]
```

其他特殊运算符包括`__getattr__`and `__setattr__`，它定义了类的 get 和 set 属性，`__sum__`and `__sub__`，它重载了算术运算符。对于这些运算符的使用，我们建议读者阅读有关该主题的更高级的书籍。我们已经提到了特殊运算符`__str__`和`__repr__`。



### 文件输入/输出





在 Python 中，您可以使用以下命令打开并写入文件：

```
>>> file = open('myfile.txt', 'w')
>>> file.write('hello world')
>>> file.close()
```

同样，您可以使用以下命令从文件中读取：

```
>>> file = open('myfile.txt', 'r')
>>> print file.read()
hello world
```

或者，您可以使用“rb”以二进制模式读取，使用“wb”以二进制模式写入，并使用标准 C 表示法以附加模式“a”打开文件。

该`read`命令采用可选参数，即字节数。您还可以使用 跳转到文件中的任何位置`seek`。

您可以从文件中读回`read`

```
>>> print file.seek(6)
>>> print file.read()
world
```

您可以使用以下命令关闭文件：

```
>>> file.close()
```

在称为 CPython 的 Python 标准发行版中，变量是引用计数的，包括那些持有文件句柄的变量，因此 CPython 知道当打开的文件句柄的引用计数减少到零时，文件可能会关闭并且变量处置。但是，在 Python 的其他实现如 PyPy 中，使用垃圾收集而不是引用计数，这意味着有可能一次累积太多打开的文件句柄，导致*gc*有机会之前出错关闭并处理所有这些。因此，最好在不再需要文件句柄时显式关闭它们。 *web2py*提供了两个辅助函数`read_file()`，`write_file()`在`gluon.fileutils`封装文件访问并确保正在使用的文件句柄正确关闭的命名空间。

> 使用 web2py 时，不知道当前目录在哪里，因为这取决于 web2py 是如何配置的。该变量`request.folder`包含当前应用程序的路径。路径可以与 command 连接`os.path.join`，如下所述。



### `exec`,`eval`





与 Java 不同，Python 是一种真正的解释型语言。这意味着它能够执行存储在字符串中的 Python 语句。例如：

```
>>> a = "print 'hello world'"
>>> exec(a)
'hello world'
```

刚刚发生了什么？该函数`exec`告诉解释器调用自身并执行作为参数传递的字符串的内容。也可以在字典中的符号定义的上下文中执行字符串的内容：

```
>>> a = "print b"
>>> c = dict(b=3)
>>> exec(a, {}, c)
3
```

在这里，解释器在执行 string 时，会看到(在示例中)`a`中定义的符号，但看不到或本身。这与受限环境不同，因为它不限制内部代码可以做什么；它只是定义了代码可见的变量集。`c``b``c``a``exec`

一个相关的函数是`eval`，它的工作原理非常相似，`exec`只是它希望参数评估为一个值，并返回该值。

```
>>> a = "3*4"
>>> b = eval(a)
>>> print b
12
```



### `import`



Python 的真正强大之处在于它的库模块。它们为许多系统库（通常以独立于操作系统的方式）提供了大量且一致的应用程序编程接口 (API)。



例如，如果您需要使用随机数生成器，您可以这样做：

```
>>> import random
>>> print random.randint(0, 9)
5
```

这将打印一个介于 0 和 9（包括 9）之间的随机整数，在示例中为 5。该函数`randint`在模块中定义`random`。也可以将模块中的对象导入当前命名空间：

```
>>> from random import randint
>>> print randint(0, 9)
```

或将模块中的所有对象导入当前命名空间（不鼓励）：

```
>>> from random import *
>>> print randint(0, 9)
```

或导入新定义的命名空间中的所有内容：

```
>>> import random as myrand
>>> print myrand.randint(0, 9)
```

在本书的其余部分，我们将主要使用定义在模块`os`、`sys`、`datetime`和中`time`的对象`cPickle`。

> 所有的 web2py 对象都可以通过一个名为 的模块访问`gluon`，这是后面章节的主题。在内部，web2py 使用了许多 Python 模块（例如`thread`），但您很少需要直接访问它们。

在以下小节中，我们将考虑那些最有用的模块。



#### `os`





该模块提供了操作系统 API 的接口。例如：

```
>>> import os
>>> os.chdir('..')
>>> os.unlink('filename_to_be_deleted')
```

> 某些`os`函数，例如`chdir`，不得在 web2py 中使用，因为它们不是线程安全的。

`os.path.join`非常有用；它允许以独立于操作系统的方式连接路径：

```
>>> import os
>>> a = os.path.join('path', 'sub_path')
>>> print a
path/sub_path
```

系统环境变量可以通过以下方式访问：

```
>>> print os.environ
```

这是一个只读字典。



#### `sys`





该`sys`模块包含许多变量和函数，但我们使用最多的是`sys.path`. 它包含 Python 搜索模块的路径列表。当我们尝试导入一个模块时，Python 会在`sys.path`. 如果您在某个位置安装附加模块并希望 Python 找到它们，则需要将该位置的路径附加到`sys.path`.

```
>>> import sys
>>> sys.path.append('path/to/my/modules')
```

运行 web2py 时，Python 驻留在内存中，并且只有一个`sys.path`，而有许多线程服务于 HTTP 请求。为了避免内存泄漏，最好在追加之前检查路径是否已经存在：

```
>>> path = 'path/to/my/modules'
>>> if not path in sys.path:
...     sys.path.append(path)
```



#### `datetime`





datetime 模块的使用最好通过一些示例来说明：

```
>>> import datetime
>>> print datetime.datetime.today()
2008-07-04 14:03:90
>>> print datetime.date.today()
2008-07-04
```

有时，您可能需要根据 UTC 时间而不是本地时间对数据进行时间戳记。在这种情况下，您可以使用以下功能：

```
>>> import datetime
>>> print datetime.datetime.utcnow()
2008-07-04 14:03:90
```

datetime 模块包含各种类：date、datetime、time 和 timedelta。两个日期或两个日期时间或两个时间对象之间的差异是 timedelta：

```
>>> a = datetime.datetime(2008, 1, 1, 20, 30)
>>> b = datetime.datetime(2008, 1, 2, 20, 30)
>>> c = b - a
>>> print c.days
1
```

在 web2py 中，date 和 datetime 用于在传递到数据库或从数据库返回时存储相应的 SQL 类型。



#### `time`

时间模块不同于`date`并且`datetime`因为它将时间表示为从纪元（1970 年初）开始的秒数。

```
>>> import time
>>> t = time.time()
1215138737.571
```

有关以秒为单位的时间和以`datetime`.



#### `cPickle`

这是一个非常强大的模块。它提供了可以序列化几乎任何 Python 对象的函数，包括自引用对象。例如，让我们构建一个奇怪的对象：

```
>>> class MyClass(object): pass
>>> myinstance = MyClass()
>>> myinstance.x = 'something'
>>> a = [1 , 2, {'hello':'world'}, [3, 4, [myinstance]]]
```

现在：

```
>>> import cPickle
>>> b = cPickle.dumps(a)
>>> c = cPickle.loads(b)
```

在此示例中，`b`是 的字符串表示形式`a`，并且是通过反序列化生成`c`的副本。`a``b`

cPickle 还可以对文件进行序列化和反序列化：

```
>>> cPickle.dump(a, open('myfile.pickle', 'wb'))
>>> c = cPickle.load(open('myfile.pickle', 'rb'))
```
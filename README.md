# Python面试

[TOC]



## 语言特性

### 1.说一下 Python 中 LEGB 是什么

LEGB 指的是 Python 中的变量作用域问题，其中

L：local 局部变量，仅存在函数内部，其存储位置位于栈中，其生命周期在函数结束时就会被释放。

E：enclosing 外部作用域变量，常见于闭包函数，也就是嵌套函数中的上一层函数变量。其生命周期在整个闭包函数结束时会被释放。

G：global 全局变量，作用于整个程序，其存储位置位于全局数据区中，其生命周期在程序销毁时就被释放。

B：builtins 内建模块变量，其存储于内置变量命名空间。

变量查找顺序：  

**L-E-G-B**

### 2.解释一下 Python 中的闭包函数

示例：

```
def func():
    a = b = 1
    def internal_func(c):
	if a+b == c:
	    print('True')
	else:
	    print('False')

test = func()
test(2)

# 结果为 True
```

闭包函数作为函数式编程中的一种，当含有以下几点时，便可称它为一个闭包函数。

1. 该函数是一个嵌套函数。
2. 内嵌函数必须引用外部函数的变量。
3. 该函数的返回值必须是内嵌函数名

闭包函数可以大幅提高代码复用性，使得代码性能大幅提高。  



### 3.说一下 Python 中的深拷贝，浅拷贝，如何自己实现深拷贝

1. 浅拷贝

   copy 模块下的 copy() 方法，实现一个对象的浅拷贝，list 下的分割也是一种浅拷贝，  

     

   示例：

   ```
   import copy
   
   a = [1, 2, [3, 4]]
   b = copy.copy(a)
   
   a == b
   a is b
   
   # 结果为 True，False
   
   e = [1, 2, [3, 4]]
   f = e[2]
   h = e[2][:]
   
   f == h
   f is h
   
   # 结果为 True，False
   ```

   

   浅拷贝只会拷贝对象的第一层引用，如果是复合类型，如 list 里面嵌套 list，则当内嵌 list 发生变化时，经过浅拷贝出来的对象，也会发生变化。

   示例：

   ```
   import copy
   
   a = [1, 2, [3, 4]]
   b = copy.copy(a)
   
   a == b
   a is b
   
   # 结果为 True，False
   
   c = copy.copy(a)
   
   a[1] = 0
   
   print(a)
   print(c)
   
   # 结果为 a = [1, 0, [3, 4]], c = [1, 2, [3, 4]]
   
   
   a[2].append(5)
   
   print(a)
   print(c)
   
   # 结果为 a = [1, 0, [3, 4, 5]], c = [1, 2, [3, 4, 5]]
   ```

   

2. 深拷贝

   深拷贝将拷贝对象里所有引用全部拷贝，生成一个全新的对象。

   示例：

   ```
   import copy
   
   a = [1, 2, 3, 4]
   b = copy.deepcopy(a)
   
   a == b 
   a is b
   
   # 结果为 True，False
   ```

   深、浅拷贝会根据拷贝对象的数据类型，决定是否开辟新的内存空间来存放拷贝出来的新对象。

   ```
   import copy
   
   a = [1, 2, 3, 4] # list 可变类型
   b = copy.copy(a)
   
   a == b
   a is b
   
   # 结果为 True，False
   
   c = (1, 2, 3, 4)  # tuple 不可变类型
   d = copy.copy(c)
   e = copy.deepcopy(c)
   
   c == d
   c is d
   c is e
   
   # 结果为 True，True, True
   ```

   

3. 自己实现深拷贝

   [深拷贝源码分析]()，先占坑，还未写。

   

### 4.说一说 Python 中的可变、不可变类型

Python 中通过将对象进行 HASH 来判断该对象是哪种类型，可 HASH 的为不可变类型，不可 HASH 的为可变类型。其中常见的几种如下所示：

​	可变类型：List，dict，set

​	不可变类型：int，float，string，tuple，frorzenset

也可通过是否含有`__hash__()`方法，判断该对象为何种类型，拥有该方法的为不可变类型，相反为可变类型。  



### 5.说一说 Python 中一个类的实例从创建到删除的过程

首先通过类的魔法方法`__new__()`来创建一个实例，再通过魔法方法`__init__()`来对这个实例初始化，最终删除一个实例时，Python 根据该实例的「引用计数」来判断是否释放该实例所占用的内存，当该实例的引用计数为 0 时，`__del__()`方法才会被调用，`__del__()`方法的不等同于`del`。



### 6.什么是单例，如何实现

单例是指一个类的实例有且只有一个。一般在一个类只需要使用一次之后便不在使用时使用。  



实现方式：

1. 使用`__new__()`方法

   ```
   class Singleton():
       is_instance = None
   
       def __new__(cls):
           if not cls.is_instance:
               cls.is_instance = super().__new__(cls)
               return cls.is_instance
   
           return cls.is_instance
           
   s = Singleton()
   g = Singleton()
   print(s is g)
   
   # 结果为 True
   ```

2. 使用装饰器

   ```
   def Singleton01(cls):
       is_instance = {}
       def get_instance(*args, **kwargs):
           if not is_instance:
               is_instance[cls] = cls()
               return is_instance[cls]
           return is_instance[cls]
       return get_instance
   
   
   @Singleton01
   class Myclass():
       pass
   
   s = Myclass()
   g = Myclass()
   print(s is g)
   
   # 结果为 True
   ```

3. 多线程下实现单例

   ```
   class Singleton02():
       is_instance = None
       is_lock = threading.Lock()
   
       def __new__(cls):
           with cls.is_lock:
               if not cls.is_instance:
                   cls.is_instance = super().__new__(cls)
                   return cls.is_instance
   
               return cls.is_instance
   
   
   s = Singleton()
   g = Singleton()
   print(s is g)
   
   # 结果为 True
   ```

4. 多线程优化

   ```
   class Singleton03():
       is_instance = None
       is_lock = threading.Lock()
   
       def __new__(cls):
           # 单例判断提前,只在第一次判断时加锁，避免无意义的加锁解锁
           if not cls.is_instance:
               with cls.is_lock:
                   cls.is_instance = super().__new__(cls)
                   return cls.is_instance
   
           return cls.is_instance
   
   s = Singleton03()
   g = Singleton03()
   print(s is g)
   
   # 结果为 True
   ```

  

------

**第二次更新**



### 7.说一下 Python 中的装饰器

原理：利用闭包，将目标函数外面再套一层函数，使得目标函数具有一个新的功能，并且不改变目标函数原有功能。  



实现方式：

1. 闭包

   ```
   def decorate(func):
       def wrapper():
           print('新功能')
           func()
           
       return wrapper
   
   def func():
       print('原有功能')
   
   f = decorate(func)
   
   f()
   
   # 结果为：
   新功能
   原有功能
   ```

2. @ 语法糖

   ```
   def decorate01(func):
       def wrapper():
           print('新功能')
           func()
           
       return wrapper
   
   @decorate01
   def func01():
       print("原有功能")
   
   func01()
   
   # 结果为：
   新功能
   原有功能
   ```

​     

### 8.说一说类属性、实例属性、私有属性和保护属性

类属性相当于全部变量，所有由类创建出来的实例，都可以使用，而实例属性相当于局部变量，只能由该实例自己使用，当类属性与实例属性命名一样时，在调用属性该属性时，会屏蔽掉类属性，而是调用实例属性，这点跟 LEGB 很像。



当通过实例对象来修改类属性时，其实修改的并不是类属性，而是新建了一个跟类属性名一样的实例属性。



Python 中将以两个下划线`__`开头，但不以两个下划线结尾的变量，认作为私有属性，Python 通过 `name manage`算法，将该私有属性的引用更改为`_classname_reference`，这样在用户试图调用该私有属性时，会因为对象引用不一样而找不到该属性，故而实现了「属性私有化」。

[^其他两种带有下划线的命名方式]: 以双下划线开头和结尾的变量，被称为魔法方法，以单下划线开头的变量，称为私有属性，但这些都是程序员默认的一种命名方法并不具有实际意义



在获取实例属性时，一般采用定义一个实例方法的方式获取属性，避免直接对实例属性进行操作，起到一个保护属性的作用。



### 9.为什么说 Python 是动态语言，鸭子类型是指什么

Python 相当于其他静态语言，可以在代码运行过程中，改变变量的属性。



鸭子类型指的是 Python 不用定义变量类型，只要该变量像是什么类型，那么就认为它就是什么类型，我们更多关注的是它的行为，而不是他的类型。



### 10.元类是什么

实例都是由类创建出来，而类则是由元类创建出来。他们之间的关系相当于「奶奶-妈妈-孙子」。  



示例：

```
class Myclass():
    pass


new = type('NewClass', (Myclass,), {'name': 'new'})

print(new)
print(new.__mro__)  # 查看该类的继承情况

# 结果为
<class '__main__.NewClass'>
(<class '__main__.NewClass'>, <class '__main__.Myclass'>, <class 'object'>)
```



具体点的内容可以参考这篇问答：[什么是元类](<https://stackoverflow.com/questions/100003/what-are-metaclasses-in-python>)



### 11.@staticmethod 和 @classmethod 的区别

  @staticmethod 是为类添加一个静态方法

  @classmethod 是为类添加一个类方法

   

### 12.如何动态添加属性

由于 Python 的特性使得程序在运行过程中，我们可以为某个对象添加属性、方法。

  

示例：

```
class Myclass:
    pass


m = Myclass()

# 为实例动态添加一个属性
m.name = 'new_atribute'

def func(self):
    return 'new_function'

# 为实例动态添加一个方法
m.func = func

print(m.name)
print(m.__dict__) # 返回 m 的所有属性,方法

# 结果为
new_atribute
new_function
{'func': <function func at 0x7f9452be12f0>, 'name': 'new_atribute'}

# 另外一种动态添加方法

import types

def func01(self):
	print('new_function01')

# 将实例 m 添加一个属性 func，而这个属性指向func()函数，故当调用 m.func 时，也就相当于调用了 func() 函数，间接实现了为 m 添加方法 func()。
m.func = types.MethodType(func, m)

print(m.func())

# 结果为 new_function01
```



### 13.对于迭代器和生成器你知道哪些，它们分别应用于什么场景

先介绍什么是可迭代的`Iterable`：任何可用于`for`循环的都是可迭代的。也可以使用`collection`模块下的`isinstance(obj, Iterable)`来检验该对象是否可迭代。  



示例：

```
from collections import Iterable


print(isinstance('abc',Iterable)) 
print(isinstance(123,Iterable)) 

# 结果为 True，False
```



迭代器：任何可以使用`next()`函数的都是迭代器，也可使用`iter()`函数将可迭代对象变为迭代器。  



示例：

```
from collections import Iterator


itr = iter('abc')

print(type(itr))
print(isinstance(itr,Iterator)) 

# 结果为 Iterator，True
```



生成器：任何函数中含有`yield`关键字的都是生成器，列表生成式中的`[]`改为`()`也是一个生成器。



示例：

```
g = [i for i in range(10)]

print(type(g))

# 结果为 list

g1 = (i for i in range(10))

print(type(g1))

# 结果为 generator

def func():
    for i in range(10):
        yield i

f = func()
print(f)

# 结果为 <generator object func at 0x7f92f5294048>
```



生成器怎么取值，什么时候结束。



生成器可以通过`next(f)`、`f.__next__()`和`f.send()`三种方式来取值，



示例：

```
def func01():
    for i in range(10):
        yield i

f = func()
print(next(f))
print(f.__next__())
print(f.send('hahah'))

# 结果为 
0
1
2
```



其中`f.send()`可以向生成器传值，但是其第一次传入的值默认为`None`。如果想要取出`send('hahah')`里传入的值，则需要在生成器中添加接收的变量。



示例：

```
def func():
    for i in range(10):
        # yield i
        temp = yield i
        print(temp)
        
f = func()

print(next(f))
print(f.__next__())
print(f.send('hahah'))  

# 结果为
0
None
1
hahah
2
```



生成器里的值被取完之后，或者中间遇到 `return`关键字，就会退出，这三种方法有一个共同点：当生成器取完之后会抛出`StopIteration`的错误。



而使用`for`循环来取出生成器里的值就不会抛出错误，这也是最被推荐的。



应用场景：在生成一个包含很多数（百万级别）的列表时，但是又只用得到其中很小一部分的数时，使用列表生成式会极大的浪费内存，且不一定能够生成，因为受机器内存限制。  

而使用生成器则不然，生成器只是在你需要的时候，才会申请一块内存，可以边取边用，极大的降低了内存消耗。



生成器用的最多的地方在于「协程」。由于基于 C 解释器下的 Python 中含有一个 GIL 锁，使得 Pyhon 的多线程是一个假性多线程，这也是为什么很多人说 Python 性能慢的原因。



### 14.说一下进程、线程、以及多任务（多进程、多线程和协程）

* 进程

  * 概念

    一个程序对应一个进程，这个进程被叫做主进程，而一个主进程下面还有许多子进程。

  * 实现方式

    1. fork() 
    2. mutiprocessing.Process
    3. mutiprocessing.Pool

  * 优缺点

    1. `fork()`是计算机最底层的进程实现方式，一个`fork()`方法创建出来的进程有两个：主进程、子进程。

       `fork()`创建出来的进程，主进程不会等待子进程。

       示例：

       ```
       import os
       
       
       print('current_pid :%d' % os.getpid())
       
       res = os.fork()
       
       # 子进程返回的是 0
       if res == 0:
       	print('res: %d' % res)
       	print('sub_pid: %d' % os.getpid())
       
       # 主进程返回的是子进程的 pid
       else:
       	print('main_pid: %d' % os.getpid())
       	print('res：%d' % res)
       
       # 结果为
       current_pid :12775
       main_pid: 12775
       res：12776
       res: 0
       sub_pid: 12776
       ```

    2. multiprocessing.Process

       `multiprocessing`模块通过将`fork`方法封装成一个`Process`类，该类有一个`start()`方法，当调用该方法时，会自动调用`run()`方法，开启一个进程。并且由`Process`创建出来的进程，可以使用`join()`方法，使得主进程堵塞，被迫等待子进程。



*未写完，下一次更新补上*

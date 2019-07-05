# Python面试

## 语言特性

* 说一下 Python 中 LEGB 是什么

  LEGB 指的是 Python 中的变量作用域问题，其中

  L：local 局部变量，仅存在函数内部，其存储位置位于栈中，其生命周期在函数结束时就会被释放。

  E：enclosing 外部作用域变量，常见于闭包函数，也就是嵌套函数中的上一层函数变量。其生命周期在整个闭包函数结束时会被释放。

  G：global 全局变量，作用于整个程序，其存储位置位于全局数据区中，其生命周期在程序销毁时就被释放。

  B：builtins 内建模块变量，其存储于内置变量命名空间。

    

  变量查找顺序：  

  **L-E-G-B**

  

* 解释一下 Python 中的闭包函数

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

  

* 说一下 Python 中的深拷贝，浅拷贝，如何自己实现深拷贝

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

     

* 说一说 Python 中的可变、不可变类型

  Python 中通过将对象进行 HASH 来判断该对象是哪种类型，可 HASH 的为不可变类型，不可 HASH 的为可变类型。其中常见的几种如下所示：

  ​	可变类型：List，dict，set

  ​	不可变类型：int，float，string，tuple，frorzenset

  也可通过是否含有`__hash__()`方法，判断该对象为何种类型，拥有该方法的为不可变类型，相反为可变类型。  

  

* 说一说 Python 中一个类的实例从创建到删除的过程

  首先通过类的魔法方法`__new__()`来创建一个实例，再通过魔法方法`__init__()`来对这个实例初始化，最终删除一个实例时，Python 根据该实例的「引用计数」来判断是否释放该实例所占用的内存，当该实例的引用计数为 0 时，`__del__()`方法才会被调用，`__del__()`方法的不等同于`del`。

  

* 什么是单例，如何实现

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
         """多线程下 __new__ 方法实现单例(优化)"""
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

     










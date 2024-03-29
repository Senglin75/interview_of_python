Python面试




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

* 可变类型：List，dict，set

* 不可变类型：int，float，string，tuple，frorzenset

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


### 8.说一说类属性、实例属性、私有属性和保护属性

类属性相当于全部变量，所有由类创建出来的实例，都可以使用，而实例属性相当于局部变量，只能由该实例自己使用，当类属性与实例属性命名一样时，在调用该同名属性时，会屏蔽掉类属性，调用实例属性，这点跟 LEGB 很像。

当通过实例对象来修改类属性时，其实修改的并不是类属性，而是新建了一个跟类属性名一样的实例属性。

Python 中将以两个下划线`__`开头，但不以两个下划线结尾的变量，认作为私有属性，Python 通过 `name manage`算法，将该私有属性的引用更改为`_classname_reference`，用户试图调用该私有属性时，会因为对象引用不一样而找不到该属性，故而实现了「属性私有化」。

[^其他两种带有下划线的命名方式]: 以双下划线开头和结尾的变量，被称为魔法方法，以单下划线开头的变量，称为私有属性，但这些都是程序员默认的一种命名方法并不具有实际意义。



在获取实例属性时，一般采用定义一个实例方法的方式获取属性，避免直接对实例属性进行操作，起到一个保护属性的作用。



### 9.为什么说 Python 是动态语言，鸭子类型是指什么

Python 相当于其他静态语言，可以在代码运行过程中，改变变量的属性。

鸭子类型指的是 Python 不用定义变量类型，只要该变量像是什么类型，那么就认为它就是什么类型，我们更多关注的是它的行为，而不是它的类型。



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

 * @staticmethod 是为类添加一个静态方法

 * @classmethod 是为类添加一个类方法

   

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

# 实例 m 添加一个属性 func，而这个属性指向func()函数，故当调用 m.func 时，也就相当于调用了 func() 函数，间接实现了为 m 添加方法 func()。
m.func = types.MethodType(func, m)

print(m.func())

# 结果为 new_function01
```



### 13.对于迭代器和生成器你知道哪些，它们分别应用于什么场景

先介绍什么是可迭代的`Iterable`：

任何可用于`for`循环的都是可迭代的。也可以使用`collection`模块下的`isinstance(obj, Iterable)`来检验该对象是否可迭代。  

示例：

```
from collections import Iterable


print(isinstance('abc',Iterable)) 
print(isinstance(123,Iterable)) 

# 结果为 True，False
```

* 迭代器

    任何可以使用`next()`函数的都是迭代器，也可使用`iter()`函数将可迭代对象变为迭代器。  

    示例：

    ```
    from collections import Iterator
    
    
    itr = iter('abc')
    
    print(type(itr))
    print(isinstance(itr, Iterator)) 
    
    # 结果为 Iterator，True
    ```

* 生成器

    任何函数中含有`yield`关键字的都是生成器，列表生成式中的`[]`改为`()`也是一个生成器。

    生成器在启动后，如果程序运行到`yeild`关键字时，会从当前程序退出，并返回`yeild`关键后面的内容。

    * 实现方式
    
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
        
        # 生成生成器
        f = func()
        # 启动生成器，并接受 yeild 后面的值
        res = next(f)
        print(f)
        print('return content: %d' % res)
        
        # 结果为
        <generator object func at 0x7f92f5294048>
    	return content: 0
        ```
    
    * 生成器怎么取值，什么时候结束  
        生成器可以通过`next(f)`、`f.__next__()`和`f.send()`三种方式来取值   
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

        其中`f.send()`可以向生成器传值，但是其第一次传入的值默认为`None`。如果想要取出`send()`里传入的值，则需要在生成器中添加接收的变量。 

        示例：
        ```
        def func():
            content = '200 OK'
            while True:
                temp = yield content
                print(temp) 

        f = func()
        # 传入 None 来启动生成器
        f.send(None)       
        # 传入参数给生成器
    	res = f.send('END')

        print(res)  

        # 结果为
        END
        200 OK
        ```

        生成器里的值被取完之后，或者中间遇到`return`关键字，就会退出，这三种方法有一个共同点：当生成器取完之后会抛出`StopIteration`的错误。       
        而使用`for`循环来取出生成器里的值就不会抛出错误，这也是最被推荐的。
    
    * 应用场景  
        在生成一个包含很多数（百万级别）的列表时，但是又只用得到其中很小一部分的数时，使用列表生成式会极大的浪费内存，且不一定能够生成，因为受机器内存限制。     
        而使用生成器则不然，生成器只是在你需要的时候，才会申请一块内存，可以边取边用，极大的降低了内存消耗。 
        生成器用的最多的地方在于「协程」。由于基于 C 解释器下的 Python 中含有一个 GIL 锁，使得 Pyhon 的多线程是一个假性多线程，这也是为什么很多人说 Python 性能慢的原因。
    
      

------

**第三次更新**



### 14.说一下进程、线程、以及多任务（多进程、多线程和协程）

* 进程
	* 概念

      一个程序对应一个进程，这个进程被叫做主进程，而一个主进程下面还有许多子进程。

	* 实现方式
		* fork()

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
		
		* multiprocessing.Process
		
		  示例：
		
		  ```
		  from multiprocessing import Process
		  import os, time
		  
		    
		  print('man_process pid : %d' % os.getpid())
		    
		  class NewProcess(Process):
		      def __init__(self):
		          Process.__init__(self)
		    
		      def run(self):
		          time.sleep(3)
		          print('%d process was runing' % os.getpid())
		    
		  np = NewProcess()
		  np.start()
		    
  		  # 结果为
		  man_process pid : 7846
		  7847 process was runing
  		```
		
		* multiprocessing.Pool
			* 同步（apply）
		
			  示例：
		  
		      ```
		      from multiprocessing import Pool
		      import time, os, random  
		    
		    
		      print('main_process pid: %d' % os.getpid())
		    
		      def run():
		          time.sleep(random.random())  # random.random() 随机生成一个小于 1 的浮点数
		          print('%d process was runing' % os.getpid())
		    
		      p = Pool(3)
		    
		      for i in range(4):
		          p.apply(run, args=())
		    
		      p.close()
		      print('waiting for sub_process')
		    
		      while True:
		          # 获取 Pool 中剩余的进程数量
		          count = len(p._cache)
		          if count != 0:
		              print('there was %d sub_process' % count)
		              time.sleep(random.random())
		          else:
		              break
		            
		      print('sub_process has done')
		    
		      # 结果为
		      main_process pid: 4295
    		  4297 process was runing
		      4296 process was runing
    		  4298 process was runing
		      4297 process was runing
		      wating for sub_process
		      sub_process has done
		      ```
			
			* 异步（apply_async)
			
			  示例：
			  
			  ```
			  from multiprocessing import Pool
			  import time, os, random
			      
			      
			  print('main_process pid: %d' % os.getpid())
			      
			  def run():
		      	# random.random() 随机生成一个小于 1 的浮点数
			      time.sleep(random.random())  
			      print('%d process was runing' % os.getpid())
			    
			  p = Pool(3)
			      
			  for i in range(4):
			      p.apply_async(run, args=())
			      
			  p.close()
			      
		      while True:
			      # 获取 Pool 中剩余的进程数量
			      count = len(p._cache)
			      if count != 0:
	  	              print('there was %d sub_process' % count)
                      time.sleep(random.random())
			      else:
	  	          break
			              
			  print('wiating for sub_process..')
		      p.join()
	  	      
			  print('sub_process has done')
		        
			  # 结果为
	  		  main_process pid: 4342
			  wiating for sub_process..
		      there was 4 sub_process
			  4344 process was runing
			  there was 3 sub_process
		      4345 process was runing
			  4344 process was runing
			  4343 process was runing
		      sub_process has done
			  ```
			  
			  
		
	* 优缺点
		* `fork()`是计算机最底层的进程实现方式，一个`fork()`方法创建出来的进程有两个：主进程、子进程。`fork()`创建出来的进程，主进程不会等待子进程。
		* `multiprocessing`模块通过将`fork`方法封装成一个`Process`类，该类有一个`start()`方法，当调用该方法时，会自动调用`run()`方法，开启一个进程。并且由`Process`创建出来的进程，可以使用`join()`方法，使得主进程堵塞，被迫等待子进程。
		* `multiprocess`下另一种开启进程的方式是通过`Pool`进程池来实现。进程池可以开启多个进程来执行多个任务，但是进程数最大不会超过系统 CPU 核数。同样的，由`Pool`创建出来的进程，主进程也不会等待子进程，通过`join()`方法可以迫使主进程等待子进程，或者使用`apply()`同步的方式。
	
	* 进程通信

		进程之间的通信可以通过队列（Queue）来进行，多个进程一部分向队列里写入数据，一部分从队列里读取数据，从而完成多进程之间的通信问题。
	
		示例：
		
	  ```
	  from multiprocessing import Process, Queue
	  import random, time, os
	  
	  
	  def write(q):
	      if not q.full():
	          for i in range(4):
	              q.put(i)
	              print('%d was writing data[%d] to queue' % (os.getpid(), i))
	              time.sleep(random.random())
	      else:
	          print('queue is full')
		
	  def read(q):
		  # 等待队列被写入数据
		  time.sleep(random.random())
		  while True:
		      if not q.empty():
		          data = q.get()
	              print('%d was reading data{%d} from queue' % (os.getpid(), data))
		      else:
		           print('queue is empty')
		           break
		  
	  # 创建通信队列,进程之间,全局变量不共享
      q = Queue()
      pw = Process(target=write, args=(q,))
	  pr = Process(target=read, args=(q,))
		  
	  pw.start()
		pr.start()
		  
	  pw.join()
	  pr.join()
	  print('end')
		  
	  # 结果为
	  4640 was writing data[0] to queue
	  4640 was writing data[1] to queue
	  4640 was writing data[2] to queue
	  4641 was reading data{0} from queue
	  4641 was reading data{1} from queue
	  4641 was reading data{2} from queue
	  queue is empty
	  4640 was writing data[3] to queue
	  end
	  ```
		
	  由于进程的执行顺序问题，造成了 pr 先于 pw 执行，所以 pr 未读取到数据，pr 进程任务结束，堵塞解开，主进程继续向下运行，最后 pw 任务结束。
		
	* 进程通信改良
	
		示例：
	
	  ```
	  from multiprocessing import Process, Queue
	  import random, time, os
	  
	  
	  def write(q):
	      if not q.full():
	          for i in range(4):
	              q.put(i)
	              print('%d was writing data[%d] to queue' % (os.getpid(), i))
	              time.sleep(random.random())
	      else:
	          print('queue is full')
	  
	  def read(q):
	      # 等待队列被写入数据
	      time.sleep(random.random())
	      while True:
	          data = q.get()
	          print('%d was reading data{%d} from queue' % (os.getpid(), data))
	  
	  # 创建通信队列,进程之间,没有全局变量共享之说
	  q = Queue()
	  pw = Process(target=write, args=(q,))
	  pr = Process(target=read, args=(q,))
	  
	  pw.start()
	  pr.start()
	  
	  pw.join()
	  # pr 进程立刻结束
	  pr.terminate()
	  print('end')
	  
	  # 结果为
	  12898 was writing data[0] to queue
	  12898 was writing data[1] to queue
	  12898 was writing data[2] to queue
	  12899 was reading data{0} from queue
	  12899 was reading data{1} from queue
	  12899 was reading data{2} from queue
	  12898 was writing data[3] to queue
	  12899 was reading data{3} from queue
	  end
	  ```
	  
	  
	
* 线程
	* 概念

	  线程是进程下的一部分，进程下负责执行代码程序的就是线程，一个进程下会有很多个线程。同样的，一个主线程下面也有很多子线程。
	
	  另外，Python 中的线程依据的是 Java 中的线程模型，如果有兴趣的同学可以研究一下。

	* 实现方式

	  示例：
	
	  ```
	  import threading, time
	  
	  
	  def run():
	      time.sleep(1)
	      # currentThread() 返回的是当前的线程对象信息
	      print('%s was runing' % threading.currentThread())
	      print('current thread\'name: %s' % threading.currentThread().getName())
	  
	  # 创建一个线程
	  t = threading.Thread(target=run, args=())
	  
	  # 启动线程
	  t.start()
	  
	  # get_ident 返回的是当前线程对象所在的内存地址（id）,该地址是唯一可以验证线程的数据
	  # 也可使用 currentThread().getName() 来简单的区分线程
	  print('current thread\'name: %s' % threading.currentThread().getName())
	  print('main_thread tid: %s' % threading.get_ident())
	  
	  # 结果为
	  current thread'name: MainThread
	  main_thread tid: 140427132020480
	  <Thread(Thread-1, started 140427100555008)> was runing
	  current thread'name: Thread-1
  	```
	  
	* 线程通信
	
	* 通信队列
	
	  通信队列作为相对来说最为安全的线程通信手段，其中`Queue`模块自身拥有所有所需的锁，这使得通信队列中的对象可以安全的在多线程之间共享。
	
	  	  这里用常见的「生产者-消费者模型」来介绍。
			
	  	  示例：
			
	  	  ```
		  import threading, queue, time, random
		    
		    
	      flag = object()
		
		  def producter(q):
		      for i in range(4):
		          q.put(i)
		          print('%s put data{%d} in queue' % (threading.currentThread().getName(), i))
		          time.sleep(random.random())
		            
		      q.put(flag)
		
		  def consumer(q):
		      time.sleep(random.random())
		      while True:
		          res = q.get()
		          if res == flag:
		              q.put(flag)
		              break
		          else:
		              print('%s get data{%d} from queue' % (threading.currentThread().getName(), res))
		        
		  # 创建队列
		  q = queue.Queue()
		        
		  # 创建线程
		  pro = threading.Thread(target=producter, args=(q,))
		  con = threading.Thread(target=consumer, args=(q,))
		        
		  pro.start()
		  con.start()
		        
		  # 结果为
		  Thread-1 put data{0} in queue
		  Thread-1 put data{1} in queue
		  Thread-2 get data{0} from queue
		  Thread-2 get data{1} from queue
	  	  Thread-1 put data{2} in queue
		  Thread-2 get data{2} from queue
	  	  Thread-1 put data{3} in queue
		  Thread-2 get data{3} from queue
		  end
		    ```
			
		  这里有一个细节。在多线程下，当生产者任务完成之后，向队列`queue`里添加了一个特殊对象（终止信号）`flag`，这样当消费者从`queue`中取出任务时，当取到`flag`时，意味着所有任务被取出，并再次将`flag`添加至`queue`中，这样其他线程中的消费者在接收到这个终止信号后，也会得知当前生产者任务已经全部发布。
			
		* 轮询
	
	  通过为数据操作添加`while`循环判断，迫使线程被迫等待操作。（为了优化等待时间，应在最核心的位置添加判断条件）
			
		  示例：
				
		  ```
		  import threading
			    
			    
		  class NewThread(threading.Thread):
			  flag = 0
			  g_num = 0
			    
			  def __init__(self):
			      super().__init__()
			   
			  def run(self):
			      print('%s was runing' % threading.currentThread().getName())
	
		      if self.name == 'Thread-1':
			          self.add_num()
			          NewThread.flag = 1
			      else:
			          # 轮询
				      # Thread-2 被迫等待 Thread-1 完成任务之后才能执行
				      while True:
				          if NewThread.flag:
				              self.add_num()
				              break
				      
			  @classmethod
			  def add_num(cls):
	              global g_num
		          for i in range(1000000):
			          cls.g_num += 1
	
	          print('on the %s, g_num: %d' % (threading.currentThread().getName(), cls.g_num))
				    
		  t1 = NewThread()
		  t2 = NewThread()
				    
		  t1.start()
		  t2.start()
			    
		  # 结果为
		  Thread-1 was runing
	      Thread-2 was runing
		  on the Thread-1, g_num: 1000000
		  on the Thread-2, g_num: 2000000
		  ```
		
		* 互斥锁
	
	  互斥锁是专门为了针对线程安全而设计的一种结构，锁可以强制线程排序，保护线程安全，但是加锁、解锁会消耗系统 CPU 资源。
		
		* 互斥锁优化
		  
		  示例：
		  
		  ```
		  import threading
		      
		      
		  class NewThread(threading.Thread):
		      g_num = 0
		      # 生成锁对象
		      lock = threading.Lock()
		      
		      def __init__(self):
		          super().__init__()
		      
		      def run(self):
		     	  # 判断当前线程是否上锁，若未上锁，则一直尝试上锁（acquire）直至成功
		          with NewThread.lock:
		              print('%s was runing' % self.name)
		              self.add_num()
		      
		      @classmethod
		      def add_num(cls):
		          for i in range(1000000):
		              cls.g_num += 1
	                    
                  print('on the %s g_num: %d' % (threading.currentThread().getName(), cls.g_num))
  	      
	      t1 = NewThread()
	      t2 = NewThread()
	  
	      t1.start()
	      t2.start()
	    ```
	  ```
		
	  # 结果为
		  Thread-1 was runing
		    on the Thread-1 g_num: 1000000
	    Thread-2 was runing
		  on the Thread-2 g_num: 2000000
		```
		
	* 死锁问题
	  
		当多线程下出现多个锁，判断条件又是另一个线程里的锁时，就会出现一种情况：当另一个线程任务执行时间过长，或是线程结束，未解锁。当前线程由于迟迟无法上锁，程序始终阻塞，此时就会陷入死锁问题。
		
	* 死锁问题解决
	  
		* 设置超时时间`threading.Lock().acquire(timeout=3)`只要在上锁时设置超时时间`timeout=`，只要超过时间，线程就会不再等待是否解锁，而是直接运行。但是这种方式很危险，可能会带来大量的等待时间。
		  * 为每个锁添加一个特殊编号，多线程在获取锁的时候严格按照该编号的升序方式来获取，相当于为线程排序，这样就避免了多线程因为资源争抢，而陷入死锁的可能。	
		  * [银行家算法](https://zh.wikipedia.org/wiki/%E9%93%B6%E8%A1%8C%E5%AE%B6%E7%AE%97%E6%B3%95)
		  
		* 独立全局变量
		  
		    多线程之间进行通信，但是又得使得两个线程之间的变量都具有独立性，每个线程从全局变量中得到的数据都不一样。
		  
		    三种方法 :
		  
		    * 传参
		    * 字典
		    * 特殊全局变量（`threading.local()`）
		  ```
		  
		  ```
	
* 进程与线程的区别
  * 线程和进程的执行顺序都是一样的，都是由操作系统的调度算法决定，不是根据程序的编写顺序来决定。 
  * 进程是资源分配的单位，而线程是 CPU 调度的单位。
  * 进程在主程序结束后，程序立马结束,需要手动利用`join()`方法使得主程序发生堵塞，来等待子进程。而主线程的任务结束后，程序会等待子线程结束才会结束。故不需要特意使用`join()`方法来使主线程等待子线程。
  * 多进程适合 CPU 密集型，多线程适合 I/O 密集型。

* 协程

	* 概念

	  线程下的一种，也叫微线程，单线程自身控制切换任务时机，达到多任务的效果。避免了由于系统在处理多进程或者多线程时，切换任务时需要的等待时间。这一点很像操作系统里的中断。

	* 实现方式

		* 生成器（yield）

		  生成器相关内容可看问题 13。
    
	      这里以一个简单的「生产者-消费者模型」来解释如何使用生成器实现协程。

      	  示例：
      
	      ```
	      import threading  
	      
	      
	      def producter(c):
	          next(c)
	          n = 4
	          print('%s was running' % threading.currentThread().getName())
	          while n:
	              print('product data: %d' % n)
	              res = c.send(n)
	              print(res)
	              n -= 1
	          print('sale out')
	          
	      def consumer():
	          res = ''
	        
	          print('%s was running' % threading.currentThread().getName())
	          while True:
	              n = yield res
	        
	              print('consume data: %d' % n)
	              res = '200 OK'
	        
	      print('%s was running' % threading.currentThread().getName())
	      c = consumer()
	        
	      producter(c)
	        
	      # 结果为
	      MainThread was running
	      MainThread was running
	      MainThread was running
	      product data: 4
	      consume data: 4
	      200 OK
	      product data: 3
	      consume data: 3
	      200 OK
	      product data: 2
	      consume data: 2
	      200 OK
	      product data: 1
	      consume data: 1
	      200 OK
	      sale out
	      ```

          可以看到，生产者事先不知道消费者具体要消费多少数据，生产者只是一直在生产。而消费者则是利用生成器的中断特性，`consumer`函数中，程序每一次循环遇到`yield`关键字就会停下，等待`producter`函数启动生成器，再继续下一次循环。
            
          在这中间只有一个线程在运行，任务的切换时机由程序员自己控制，避免了由于多线程之间的切换消耗，这样就简单实现了协程。

		* 异步（asyncio）

		  由于生成器在未来的 Python 3.10 版本中将不在支持协程，而是推荐使用`asyncio`库，该库适用于高并发。
    
          自己目前不会，就不瞎 BB 了，具体可看文档。
            
          [asyncio 中文文档](https://docs.python.org/zh-cn/3/library/asyncio-task.html)

---
**第四次更新**


### 15.说一说 GIL

前面有提到由于 Python 基于 C 语言编写的解释器中设置了一个 GIL 全局变量锁，该锁使得 Python 的多线程在处理 CPU 计算密集型任务时，同一时刻只能有一个线程在运行，这也是为什么说  Python 的多线程是一个「假性多线程」的原因。

解决 GIL 的办法在处理 CPU 计算密集型任务时，使用多进程 + 协程，发挥计算机多核的威力，而处理 I/O 密集型，则可以使用多线程。

### 16.说一说 Python 中的垃圾回收机制（GC）

* 引用计数清零
* 标记-清除
* 隔代回收
  这里有一篇文章可以参考一下。[垃圾回收机制](https://juejin.im/post/5b34b117f265da59a50b2fbe)

### 17.说一说 Python 中的函数式编程

* lambda

  lambda 匿名函数，使用匿名函数可以帮助我们简化代码，且节省程序由于显示创建函数的消耗。

  示例：

  ```
  # 冒号左边是参数， 右边是表达式，lambda 返回的是右边表达式的结果
  L2 = lambda x : x * x
  print(L2(5))
  
  # 结果为 25
  ```

* map

  `map(func，*Iterable)`，map 函数通过将可迭代对象传入 func 函数，全部执行。

  示例：

  ```
  # 相当于 [1*1, 2*2, 3*3, 4*4]
  L2 = list(map(lambda x: x * x, range(1, 4)))
  print(L2)
  
  # 结果为
  [1, 4, 9, 16]
  ```

* reduce

  `reduce(func， *sequence)`，reduce 函数可以将函数 func 迭代调用。

  示例：

  ```
  from functools import reduce
  
  
  # 相当于 (（1*2)*3)
  L2 = reduce(lambda x， y: x * y, range(1, 4))
  print(L2)
  
  # 结果为
  6
  ```

* filter

  filter(func，*Iterable)，filter 函数通过将可迭代对象当做参数传递给 func 函数，并返回 func 函数中返回结果为 True 的值。

  示例：

  ```
  # 相当于 g = [x for x in range(10) if x < 3]
  L2 = list(filter(lambda x: x < 3, range(10)))
  print(L2)
  
  # 结果为
  [0, 1, 2]
  ```

### 18.is 和 == 的区别

* a is b 比较的是 a 和 b 的「id」是否相同
* a == b 比较的是 a 和 b 的「值」是否相同

### 19.range 和 xrange 的区别

对于现如今的 Python 3.x，这个问题其实已经没有什么意义了，由于其在各大面经中都出现过，这里简单解释下。在 Python 2.x 中，`range`和`xrange`最大的区别就是`xrange`生成的不是一个`list`，而是一个生成器，这两者的区别在问题 13 已经讲过，这里不在赘述。

Python 3.x 中已经移除了`range()`方法，并将`xrange()`方法更名为`range()`。

### 20.新式类和旧式类的区别

新式类和旧式类的变化主要讲的是 Python 中类的「继承」问题。

旧式类中，类的继承按照「从左往右，深度优先遍历」的原则。

新式类中，类的继承按照「C3 算法」的原则：如果子类在继承来自不同父类的方法是不一样的时候，会从下而上，先左后右的合并继承方法。如果有一样的方法，则会继承第一个方法然后舍弃另一个相同的方法，接着合并其他不同的方法。解决了旧式类继承中存在的二义性和单调性。

示例：

```
# 新式类

class B:
    def __init__(self,name):
        self.name = name

    def get_name(self):
        return 'B'

class C:
    def __init__(self,name):
        self.name = name

    def get_name(self):
        return 'C'

class BC(B, C):
    pass

b = B('B')
c = C('C')
bc = BC('C')

print(bc.name)
name = bc.get_name()
print(name)

# 查看 c 在搜索方法时的先后顺序
print(bc.__mro__)

# 结果为
C
B
(<class '__main__.BC'>, <class '__main__.B'>, <class '__main__.C'>, <class 'object'>)
```

另外，新式类相对于旧式类还多了这么些变化。

* `__slot__`：限制实例属性的设置范围。

* `__getattrbute__`：获取实例属性值。

* `type`与`__class__`：返回类型统一。

    

    

------

*至此，有关 Python 知识点的面试题就已告一段落，下次更新数据库（MySQL，Redis）方面面试题。*

*所有内容均已上传至「GitHub」仓库，欢迎大家[点击原文](https://github.com/Senglin75/interview_of_python.git)查阅。*

*欢迎大家关注个人微信公众号「**PythonDeveloper**」，所有内容**第一时间更新于公众号**。*

*所有内容均为本人一点一点敲出来的，难免会有错字、语句不通顺的地方，欢迎大家指正。*

*所有示例均已在 Python 3.5 环境下测试通过，如有出错地方，还请检查自身 Python 版本。*

*因本人水平有限，文章内容难免会有出错，欢迎大家评论指出，一起进步。*



---

**第五次更新「数据库篇」**

# 数据库

## MySQL

### 1.说一下 char、varchar 、text 的区别

这里先介绍一下数据库的概念，数据库是一种数据结构，内含多种算法，帮助我们将数据以最优化的方式存储在计算机中，也可以帮助我们快速找到存储的数据。

数据最终存储在计算机中都是以「二进制」的方式存储。比如 4，存储在计算机中其实是以 0100 的方式存储。比如 A，存储在计算中是以 0100 0001 的方式存储。

`char`：定长字符型，最大可存储 255 （2 的 8 次方）个字节长度，可以理解成最大可以存储 255 个字符。在计算机中以 8 位二进制的方式存储。

使用`char`类型存储数据时，假设存储的数据是 4，4 在计算机中存储的结果是 0000 0100，意味着使用定长字符型`char`，不管你存储的值是多少位，最终在计算机里都是以 8 位二进制的方式存储，不满 8 位，前面补 0。超过 8 位，超出的部分会被去除。

也就是说当使用`char`字符型存储数据后，该数据转换为二进制时的长度超过了 8 位，那么该数据将不会完整存储，会「丢失」一部分数据。

`varchar`：不定长字符型，最大可存储 65535（2 的 16 次方） 个字节长度，在计算机中以 16位 二进制的方式存储。

它与`char`不同的地方在于，当字符长度在 0-255 以内时，会在后面添加一个字节，超过 255 时，添加两个字节。同样的，当超过最大存储长度后，也会丢失一部分数据。

`text`：长文本数据类型，最大可存储 65555 个字节长度，不能指定长度，也就是说不支持`text(num)`。

但是该类型尽量不要使用，因为`text`类型数据在检索中，不会使用索引，而是使用全局搜索，这会产生临时表，使得检索时间变长，不推荐使用。

由于`char`和`varchar`的特性，在实际使用当中，如果该数据是经常会发生变化、经常使用的，那么推荐使用`char`类型，因为 MySQL 在对数据进行排序时，会根据该数据的长度来排，固定长度的`char`类型会提供更高的性能。但是由于固定长度的特性，在存储短数据时，一定程度上也会造成资源浪费，算是一个双刃剑。

### 2. varchar（100）中的 100 有什么意义

100 只是在呈现角度上定义的，比如该数据有 120  个字符，那么你在查询该数据时，看到的只有 100 个。但是如果在定义时，添加了`UNSIGNED ZEROFILL`属性，那么这将改变该类型的最大存储长度。

同样的，在实际使用当中，`varchar(num)`里的值不需要定义的特别长，只要够用就行，具体原因上面有提，这里不再赘述。

### 3. 说一说 DROP、DELETE、TRUNCATE 的区别

`DORP`：非事务操作，彻底删除一张表，无法反悔恢复。

`DELETE`：事务操作，删除表里的一行或多行数据，如果反悔或是误删，可以通过「事务回滚」恢复该表。不会影响该表下的`view`或索引。

`TRUNCATE`：非事务操作，删除表里的某行数据，或是删除整张表的数据（表依然存在，只是成了一张空表）。无法反悔恢复，并且会将该表下的`view`或索引重置。

执行速度：DROP > TRUNCATE > DELETE。

### 4. 说一说 MySQL 三范式

**第一范式：**表中的字段只能表达一种意思，不能模棱两可。

**第二范式：**表必须含有一个唯一主键来标识这张表。

**第三范式：**表中的字段不能互相依赖。

### 5. 说一说 MySQL 中如何分区、分表

Scale Out（垂直切分）

Scale Up（横向拆分）

这里有篇文章值得看一看。[MySQL 分区、分表](https://blog.csdn.net/KingCat666/article/details/78324678)

### 6. 了解索引吗

如果把数据库当做一本书的话， 那么索引就是书的「目录页」，通过目录，我们可以快速定位查找内容，同样的，目录页在书中也占了一页纸，所以索引是一个数据结构，也要占据数据库物理内存。

索引分为 4 种类型：普通索引、唯一索引、主键索引和全文索引（MyISAM 专有）。

索引的创建规则：经常使用的字段名，和出现在 where 后面的字段名，建议为它们创建索引，索引要遵循最左前缀原则（最能体现该索引特征，也就是常用的字段放最左边）。

索引的原理：可以看看这篇文章。[索引](<http://database.51cto.com/art/201907/599144.htm>)

索引的使用场景：中等、大量数据时，使用索引效率会非常高，小型数据不建议使用索引，没有全局搜索来的快。

索引的作用：索引可以提高查询速度。但是索引会增加数据库存储额外开销。索引会将数据库查询时的随机 I/O 变成顺序 I/O，减少服务器排序操作，和临时表的开销。

### 7. 说一下常用的 MySQL 优化手段

* 使用`EXPLAIN`查看 SQL 执行计划，帮助自己查看哪些地方可以优化。
* 杜绝使用 `SELECT * FROM xxx` 这种查询语句，需要什么就查什么。
* 尽量不要使用`text`这种类型，这会使得数据查询该字段时，创建临时表。
* 明确知道查询数据结果大概有几行时，使用`LIMIT`，为查询结果限制显示页数。
* 避免使用 MySQL 的内置函数。
* 尽量使用 `EXISTS`和`BETWEEN`代替`IN`。
* 避免在 `WHERE`中使用表达式操作，这会使得 MySQL 放弃使用索引查询。
* 尽量使用小表驱动大表（从小的表中，查找跟大表中有关系的数据），可以减少 CPU 运算次数，以及 I/O 总量。
* 尽量使用`INNER JOIN`而不是`LEFT JOIN`，因为前者默认使用小表驱动大表。
* 索引要遵循最左前缀法则。
* 避免使用模糊查询`LIKE`。
* 避免设置字段`NULL`属性，在对`NULL`进行判断时，会使得 MySQL 放弃使用索引。

### 8. InnoDB 和 MyISAM 的区别

* InnoDB 支持外键，MyISAM 不支持。
* MyISAM 拥有全文索引，InnoDB 没有。
* 数据库崩溃后，InnoDB 可以安全恢复，而 MyISAM 不可以。
* InnoDB 拥有事务，而 MyISAM 没有。
* InnoDB 拥有行锁，而 MyISAM 拥有表锁。
* MyISAM 计算 `COUNT(*)`时，速度远高于 InnoDB。

### 9.什么是事务

InnoDB 引擎下，MySQL 支持事务操作，事务拥有以下几个特点：

* 原子性
* 可靠性
* 稳定性
* 隔离性

使用事务的操作，要么执行，要么不执行，只有一个结果，但是事务可以回滚，也就是撤回操作。

### 10.说一下悲观锁、乐观锁

InnoDB 引擎下的 MySQL 在处理高并发时，会对 MySQL 数据库添加锁机制，以此完成并发的要求，并保证数据的完整性，可靠性。

悲观锁是 MySQL 为数据库添加行锁，强行为多个事务排序，阻塞事务运行，解决事务之间的冲突问题，但是事务之间有可能出现长时间等待，且开锁、解锁需要额外的数据库资源消耗。所以要谨慎使用。

乐观锁没有锁机制，但是引入了版本号控制，在高并发时，数据库在事务提交之前会进行版本号校验，如果版本后前后不一致，说明此刻有其他事务正在操作，那么本次事务重新操作。

版本号的好处在于没有锁的开销，并且只在事务最后提交更改时进行判断，但是也要考虑重新执行的代价是否过大。

总的来说，高并发下，读操作多的时候，使用乐观锁，写的操作时，使用悲观锁。



---

**第六次更新**



### 1.说一说 Redis 是什么

Redis 是一种「Key-Value」的内存型、非关系型数据库，属于 NoSQL 的一种。

Redis 的读写速度特别快，特别适合读写频繁的场景。

Redis 支持主从复制，支持数据持久化。

### 2.知道 Redis 有哪些常用数据类型吗

* String
* List
* Hash
* Set
* Zset

### 3.说一下 Redis 用来做什么

* 利用 String，可以很容易实现「计数器」功能。
* 利用 List ，可以实现「粉丝列表」功能。
* 利用 Hash，可以实现「浏览记录」功能。
* 利用 Set 的并集、合集特性，可以实现「共同好友」功能。
* 利用 Zset 的排序特性，可以实现「排行榜」功能。
* 由于 Redis 的高速读写特性，可以充当「缓存」。
* 充当「消息队列」，如 Celery 中就是用 Redis 充当中间人，生产者和消费者都是向 Redis 中发布任务和接受任务。

### 4.说一下 Redis 是怎样将数据持久化的

* RDB

  为某一时刻下的 Redis 数据库里的内容拍一张照片（快照），并将该照片写进磁盘中，实现数据持久化。

  当数据库崩溃时，会丢失最后一次快照的内容。

  如果数据库内容过大，那么生成快照时间会很长。

* AOF

  通过写命令，将数据追加到（Append Only File）文件末尾，实现数据持久化。

  但是使用 AOF，需要解决同步问题。

  因为文件的写入不会立马执行，而是先将写入的内容放置「缓存区」，等待操作系统决定何时将缓存区的内容写入磁盘。

  Redis 拥有三种同步策略：Always、Everysec、No。

  绝大多数情况下都是使用第二种策略，每隔一秒钟同步一次，这样就算数据库崩溃也只会损失一秒钟的数据。

  而其他两种策略，要么对数据库的压力过大，过多降低数据库的性能，要么具有丢失数据量过大的风险。

### 5.了解 Redis 主从复制吗

使用`slaveof host port`设置主从服务器。

Redis 的主从服务器有点像树结构，从服务器只能有一个主服务器。

Redis 不支持「主-主复制」。

Redis 主从复制通过利用 RDB 快照持久化的特性，将某一时刻下主服务器的内容生成快照，然后发送给从服务器，并利用缓存区保存在这期间里进行的「写命令」，等待快照发送完毕之后，再将写命令发送给从服务器。

从服务器在接收到快照之后，会将自身的数据全部丢弃，载入快照里的数据，载入完毕之后，再执行主服务器的写命令。

### 6. 说一下 Redis 和 Memcache 的区别

| Redis                              | Memcache          |
| :--------------------------------- | :---------------- |
| 支持 String、List、Hash、Set、Zset | 只支持 String     |
| 支持数据持久化                     | 不支持            |
| 支持事务                           | 不支持            |
| 支持主从复制                       | 不支持            |
| 支持分布式                         | 不支持            |
| 支持将数据交换到磁盘中             | 不支持            |
| 单进程、单线程 IO 多路复用         | 多线程、非阻塞 IO |

总结为一点，只用得到 Redis 里的 String 类型存储数据时，使用 Memcache。

除此之外，使用 Redis。

### 7. 了解 Redis 是怎样保证热数据的吗

Redis 是内存型的数据库，可以设置最大内存，当内存不够时，会使用「数据淘汰策略」，删除部分数据。

但是并不是针对 Redis 里所有的数据，而是抽样选取小部分「数据集」。

| 策略            | 描述                                                       |
| :-------------- | :--------------------------------------------------------- |
| Volatile-LRU    | 从设置了过期时间的「数据集」中，删除最近、最少使用的数据集 |
| Volatile-TTL    | 从设置了过期时间的「数据集」中，删除即将要过期的数据。     |
| Volatile-Random | 从设置了过期时间的「数据集」中，随机删除数据。             |
| Always-LRU      | 从「数据集」中删除最近、最少使用的数据。                   |
| AllKeys-Random  | 从「数据集」中随机删除数据。                               |
| Noeviction      | 禁止删除数据。                                             |

当 Redis 作为缓存使用时，为了保证缓存数据都是「热数据」，可以将 Redis 的最大内存设置为缓存的大小，并使用 Always-LRU 策略。将最近最少使用的数据舍弃，保证缓存里热数据的「高命中率」。



------

*至此，有关 「数据库」知识点的面试题就已告一段落，下次更新「网络通信方面」面试题。*

*所有内容均已上传至「GitHub」仓库，欢迎大家[点击原文](https://github.com/Senglin75/interview_of_python.git)查阅。*

*欢迎大家关注个人微信公众号「**PythonDeveloper**」，所有内容**第一时间更新于公众号**。*

*所有内容均为本人一点一点敲出来的，难免会有错字、语句不通顺的地方，欢迎大家指正。*

*因本人水平有限，文章内容难免会有出错、遗漏，欢迎大家评论指出，一起进步。*

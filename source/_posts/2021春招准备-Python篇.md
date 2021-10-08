---
title: Python篇-2021春招准备
date: 2021-02-27 12:04:26
tags:
---

#### 1、迭代器与生成器

**迭代器**

python中，任意对象，只要定义了**\_\_next\_\_**方法，它就是一个迭代器。因此，python中的容器如列表、元组、字典、集合、字符串都可以用于创建迭代器。

*for*循环从列表[1,2,3]中取元素，这种遍历过程就被称作**迭代**

```python
# 列表是迭代器
for element in [1, 2, 3]:
    print(element)
# 元组是迭代器
for element in (1, 2, 3):
    print(element)
# 字典是迭代器
for key in {'one':1, 'two':2}:
    print(key)
# 字符串是迭代器
for char in "123":
    print(char)
# 打开的text同样是迭代器
for line in open("myfile.txt"):
    print(line, end='')
```

如果你不想用for循环迭代呢？这时你可以：

- 先调用容器（以字符串为例）的iter()函数
- 再使用 next() 内置函数来调用 __next__() 方法
- 当元素用尽时，__next__() 将引发 StopIteration 异常

```python
>>> s = 'abc' 
>>> it = iter(s)
>>> it
<iterator object at 0x00A1DB50>
>>> next(it)
'a'
>>> next(it)
'b'
>>> next(it)
'c'
>>> next(it)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
    next(it)
StopIteration
```

**生成器**

> 通过列表生成式，我们可以直接创建一个列表。
> 但是，受到内存限制，列表容量肯定是有限的。
> 而且，创建一个包含100万个元素的列表，不仅占用很大的存储空间，如果我们仅仅需要访问前面几个元素，那后面绝大多数元素占用的空间都白白浪费了。
> 所以，如果列表元素可以按照某种算法推算出来，那我们是否可以在循环的过程中不断推算出后续的元素呢？
> 这样就不必创建完整的list，从而节省大量的空间。在Python中，这种一边循环一边计算的机制，称为生成器（Generator）。

**生成器也是一种迭代器**，但是你只能对其迭代一次。这是因为它们并没有把所有的值存在内存中，而是**在运行时生成值**。

使用了 **yield** 的函数被称为生成器（generator）

```python
def reverse(data):
    for index in range(len(data)-1, -1, -1):
        yield data[index]

>>> for char in reverse('golf'):
...     print(char)
...
f
l
o
g
```
**Send**

- 如果使用send函数唤醒生成器,第一次调用send的时候，传入的值为None
- 一般情况下会使用next()唤醒第一次

```python
def MyGenerator():
        value=yield 1
        yield value
        return done

gen=MyGenerator()
print(next(gen))
print(gen.send("I am Value"))


1
I am Value

send方法发送信号终止生成器
```



#### 2、is与==的区别

Python中对象包含的三个基本要素	

- id(身份标识)      is判断
- type(数据类型)
- value(值)  ==判断

```python
>>> x = y = [4,5,6]
>>> z = [4,5,6]
>>> x == y
True
>>> x == z
True
>>> x is y
True
>>> x is z
False
>>>
>>> print id(x)
3075326572
>>> print id(y)
3075326572
>>> print id(z)
3075328140
```



#### 3、\_\_init\_\_与\_\_new\_\_的区别

- \_\_new\_\_用于初始化一个实例
- \_\_init\_\_用于实例化后调用的第一个方法
- \_\_str\_\_重载print方法

```python
class Person:
    def __new__(cls, *args, **kwargs):
        # def __new__(cls, name, age):
        print('__new__called.')
        return super(Person, cls).__new__(cls)

    def __init__(self, name, age):
        print('__init__called.')
        self.name = name
        self.age = age

    def __str__(self):
        return '<Person: %s(%s)>' % (self.name, self.age)


if __name__ == '__main__':
    zfl = Person('zfl', 25)
    print(zfl)

```

**实现自定义metaclass**

```python
class PositiveInteger(int):
    def __new__(cls, value):
        return super(PositiveInteger, cls).__new__(cls, abs(value))

i = PositiveInteger(-3)
print(i)
```



**单例模式**

```python
class Singleton(object):
    def __new__(cls, *args, **kwargs):
        if not hasattr(cls, 'instance'):
            cls.instance = super(Singleton, cls).__new__(cls)
        return cls.instance


obj1 = Singleton()
obj2 = Singleton()

obj1.attr1 = 'value1'
print(obj1.attr1, obj2.attr1)
print(obj1 is obj2)

```



#### 4、垃圾回收机制(GC)

> 引用计数为主，分代回收为辅

**(a) 引用计数**: 每个对象维护一个*ob_ref*，用来记录当前对象被引用的次数

如下情况引用计数器+1:

- 对象被创建: _a=14_
- 对象被引用: *b=a*
- 对象被作为参数传递到函数中: *func(a)*
- 对象作为一个元素存储在容器中， *list = {a, 'a', 'b', 2}*

如下情况引用计数器-1:

- 对象的别名被显式销毁: *del a*
- 该对象的引用别名被赋予新的对象: *a=26*
- 一个对象离开作用阈，局部变量引用计数器-1
- 元素从容器中删除或容器被销毁

当计数器为0时被销毁

**优点:**

- 高效
- 无停顿、实时性
- 对象有确定生命周期
- 易于实现

**缺点:**

- 维护引用计数消耗资源
- 无法解决循环引用

```python3
list1 = []
list2 = []
list1.append(list2)
list2.append(list1)
```

**(b) 标记-清除**: 基于追踪回收(tracing GC)

对象之间通过引用（指针）连在一起，构成一个有向图，对象构成这个有向图的节点，而引用关系构成这个有向图的边。从根对象（root object）出发，沿着有向边遍历对象，可达的（reachable）对象标记为活动对象，不可达的对象就是要被清除的非活动对象

![title](/images/2021春招准备-Python篇/1.png)

在上图中，我们把小黑圈视为全局变量，也就是把它作为root object，从小黑圈出发，对象1可直达，那么它将被标记，对象2、3可间接到达也会被标记，而4和5不可达，那么1、2、3就是活动对象，4和5是非活动对象会被GC回收。

标记清除算法作为Python的辅助垃圾收集技术主要处理的是一些容器对象，比如list、dict、tuple，instance等，因为对于字符串、数值对象是不可能造成循环引用问题。Python使用一个双向链表将这些容器对象组织起来。

**缺点：**清除非活动的对象前它必须顺序扫描整个堆内存，哪怕只剩下小部分活动对象也要扫描所有对象

**(c)分代回收:**

分代回收是一种以**空间换时间**的操作方式，Python将内存根据对象的存活时间划分为不同的集合，每个集合称为一个代，Python将内存分为了3“代”，分别为**年轻代（第0代）**、**中年代（第1代）**、**老年代（第2代）**，他们对应的是3个**链表**，它们的垃圾收集频率与对象的存活时间的增大而减小。

新创建的对象都会分配在年轻代，年轻代链表的总数达到上限时或者超过一定的阈值，Python垃圾收集机制就会被触发，把那些可以被回收的对象回收掉，而那些不会回收的对象就会被移到中年代去，依此类推，老年代中的对象是存活时间最久的对象，甚至是存活于整个系统的生命周期内。

分代回收是建立在标记清除技术基础之上。分代回收同样作为Python的辅助垃圾收集技术处理那些容器对象

**(d)过程:**

- 分配内存
- 发现超过阈值了
- 触发垃圾回收
- 将所有可收集对象链表放到一起
- 遍历, 计算有效引用计数
- 分成 有效引用计数=0 和 有效引用计数 > 0 两个集合
- 大于0的, 放入到更老一代
- =0的, 执行回收
- 回收遍历容器内的各个元素, 减掉对应元素引用计数(破掉循环引用)
- 执行-1的逻辑, 若发现对象引用计数=0, 触发内存回收
- python底层内存管理机制回收内存



#### 5、多线程

> python3是假的多线程，它不是真真正正的并行，其实就是串行，只不过利用了cpu上下文的切换而已
>
> 只有获得GIL锁的线程才能真正在cpu上运行。所以，多线程在python中只能交替执行，即使100个线程跑在100核cpu上，也只能用到1核。

- 线程: 线程被称为轻量级进程，是最小执行单元，系统调度的单位。线程切换需要的资源一般，效率一般。 
- 多线程: 在单个程序中同时运行多个线程完成不同的工作，称为多线程
- 并发：操作系统同时执行几个程序，这几个程序都由一个cpu处理，但在一个时刻点上只有一个程序在cpu上处理
- 并行：操作系统同时执行2个程序，但是有两个cpu，每个cpu处理一个程序，叫并行
- 串行： 是指的我们从事某项工作是一个步骤一个步骤去实施 

```python
import threading

g_num = 0


def update():
    global g_num  # global声明全局变量
    for i in range(10):
        g_num += 1


def reader():
    global g_num
    print(g_num)


t1 = threading.Thread(target=update)
t2 = threading.Thread(target=reader)
t1.start()
t2.start()
```

**Global Interpreter Lock(全局解释器锁GIL)**

某个线程想要执行，必须先拿到GIL，我们可以把GIL看作是“通行证”，并且在一个python进程中，GIL只有一个。拿不到通行证的线程，就不允许进入CPU执行。适合**IO密集型**

用multiprocessing替代Thread，多核下，想做并行提升效率，比较通用的方法是使用多进程，能够有效提高执行效率

```python
import threading
import time
global_num = 0

lock = threading.Lock()           #互斥锁

def test1():
    global global_num
    lock.acquire()
    for i in range(1000000):
        global_num += 1
    lock.release()
    print("test1", global_num)


def test2():
    global global_num
    lock.acquire()
    for i in range(1000000):
        global_num += 1
    lock.release()
    print("test2", global_num)

t1 = threading.Thread(target=test1)
t2 = threading.Thread(target=test2)
start_time = time.time()

t1.start()
t2.start()
t1.join()
t2.join()
print(global_num)
```



#### 6、函数式编程

> 允许把函数本身作为参数传入另一个函数，还允许返回一个函数

- 变量可以指向函数

```python
func_abs = abs
print(func_abs(-10))
```

- 函数可以作为函数的参数

```python
def add(x, y, f):
    return f(x) + f(y)
print(add(-5, -6, abs))

# map(一个函数，一个序列)
def f(x):
    return x * x
r = map(f, [1, 2, 3, 4, 5, 6])
print(list(r))
[1, 4, 6, 9, 16, 25, 36]

# reduce ->  reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)
from functools import reduce
def add(x, y):
    return 10 * x + y
print(reduce(add, [1, 2, 3, 4, 5]))
12345

# filter filter()也接收一个函数和一个序列. filter()把传入的函数依次作用于每个元素，然后根据返回值是True还是False决定保留还是丢弃该元素
def is_odd(n):
    return n % 2 == 1
print(list(filter(is_odd, [1, 2, 3, 4, 5, 6, 10, 15])))
[1,3,5,15]

# sorted()函数也是一个高阶函数，它还可以接收一个key函数来实现自定义的排序
sorted([36, 5, -12, 9, -21], key=abs)
[5, 9, -12, -21, 36]

```

- 返回值为函数

- 闭包: 闭包在运行时可以有多个实例，不同的引用环境和相同的函数组合可以产生不同的实例，名称相同的变量在不同环境中代表的意义不同

- 匿名函数 `lambda`:  

```python
# 冒号前面是参数，后面是返回值
list(map(lambda x: x * x, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
```

- 装饰器: decorator就是一个返回函数的高阶函数
  - 接收被装饰的函数作为参数

```python
# 装饰器无参数
def log(func):
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return wrapper
  
# 装饰器有参数
def log(text):
    def decorator(func):
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator

@log('exec')
def now():
    print('2015-3-25')
now()
```



#### 7、常见的库

- datetime
- os
- random
- math
- sys
- pandas
- numpy
- matplotlib



#### Python动态创建类

```python
from com.twowater.hello import Hello

h = Hello()
h.hello()

print(type(Hello))
print(type(h))
------------------

def printHello(self, name='Py'):
    # 定义一个打印 Hello 的函数
    print('Hello,', name)

# 创建一个 Hello 类
Hello = type('Hello', (object,), dict(hello=printHello))

# 实例化 Hello 类
h = Hello()
# 调用 Hello 类的方法
h.hello()
# 查看 Hello class 的类型
print(type(Hello))
# 查看实例 h 的类型
print(type(h))
```

在这里，需先了解下通过 `type()` 函数创建 class 对象的参数说明：

1、class 的名称，比如例子中的起名为 `Hello`

2、继承的父类集合，注意 Python 支持多重继承，如果只有一个父类，tuple 要使用单元素写法；例子中继承 object 类，因为是单元素的 tuple ，所以写成 `(object,)`

3、class 的方法名称与函数绑定；例子中将函数 `printHello` 绑定在方法名 `hello` 中

具体的模式如下：

```python
type(类名, 父类的元组（针对继承的情况，可以为空），包含属性的字典（名称和值）)
```


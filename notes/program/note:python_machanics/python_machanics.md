## Python machanics



#### 1. Class's private variable

在C++中,我们知道,内部私有变量是不能在类外被直接赋予的.

在java中,私有变量的名字一般前加 _ .而在python中,通过设置self.__variable可以设置私有变量.



#### 2. @property

这个是用来get,set类中的数据的,因为对于一般的数据的get,和set而言,我们通常不仅仅是赋值,取值这么简单,还会有限制,比如说不能设置太大的值之类的限制,需要使用set_variable().在某些情况下,我们要获取数据的某个属性,可能要用一些代码,这就有了用get_variable()函数的必要.

因此在没有property的情况下,我们通常需要使用set_variable() get_variable()这样的方法,这就使得获取和设置很麻烦.因此,property()函数横空出世.

property()函数是一个系统函数,包括:

```python
property(fget=None, fset=None, fdel=None, doc=None)
```

我们一般会设置前两个.在某些特殊情况下会设置第三个.

而porperty的使用方法,我们通常是在get_variable()函数上面使用property函数的装饰器.

例如:

```python
@property
def shape(self):
    return self.data.shape
```

那么对于set_variable()我们怎么搞呢?我们使用<item>.setter来作为装饰器,例如:

```python
@property
def shape(self):
    return self.data.shape
@shape.setter
def shape(self,value):
    if value < -273:
        raise ValueError("Temperature below -273 is not possible")
    self.data.shape = value
```



#### 3. @classmethod

classmethod 传入cls参数, 这个cls代表的是类本身:

```python
class A(object):
    value = 1
    def foo1(self):
        print "Hello",self
    @classmethod
    def foo2(cls):
        print "hello",cls
        
>>> a = A()
>>> a.foo1() # 最常见的调用方式, 返回的是一个类的实例.
Hello <__main__.A object at 0x9f6abec>
>>> A.foo1(a) # 这里传入实例a，相当于普通方法的self, 这个用法没有用过.
Hello <__main__.A object at 0x9f6abec>
>>> A.foo2() # 也就是说, cls是类本身, 这里返回的是类的抽象定义, 因此没有地址.
hello <class '__main__.A'>
>>> A # 结果和上面一样, 验证了cls确实是代表的抽象类定义.
<class '__main__.A'>
```

因此, 我们就知道了使用到 @classmethod 的函数, 是可以在不实例化类的情况下实现的. 并且, 抽象类可以调用类中不需要实例化就可以获得的数据, 例如上面的 value.

使用地方： 和类进行交互，但不和其实例进行交互的函数方法

#### 4. @staticmethod

可以由类调用, 但是不参与类的数据分享. 这个也是为抽象类量身定做的, 不会对任何实例类型进行操作.

用@staticmethod标注的函数是不会接受类的"self"参数的.

> 注意, 我们平时虽然一直都在使用self, 但是self并不是一个关键字, python的机制默认, 类中一般方法的第一个参数, 为类的实例. 也就是说不管你的第一个参数叫做啥, 他就是代表这这个类的实例. 我们只是为了标准化, 使用了self. 

```python
class Foo:
    def spam(x,y,z): #类中的一个函数，self和x啥的没有不同都是参数名
        print(x,y,z)
>>> a = Foo()
>>> a.spam(1,2)
(<__main__.Foo instance at 0x7f360449b8c0>, 1, 2)

class Foo2:
    @staticmethod
    def spam(x,y,z): #类中的一个函数，self和x啥的没有不同都是参数名
        print(x,y,z)
>>> a = Foo()
>>> a.spam(1,2)
TypeError: spam() takes exactly 3 arguments (2 given)
>>> a.spam(1,2,3)
(1, 2, 3)
```



#### 5. __str\_\_

\_\_str\_\_定义在类内部，必须返回一个字符串类型，打印由这个类产生的对象时，会触发执行.

```python
class Foo:
    def __str__(self):
        return '<name:%s,age:%s>' %("zhao xin","1996-10-21")
a = Foo()
print(a)
>>> <name:zhao xin,age:1996-10-21>
print(Foo)
>>> __main__.Foo
```



#### 6. 封装函数

##### 6.1 isinstance

isinstance(obj,cls)检查是否obj是否是类 cls 的对象. 

obj代表的是类实例, cls代表的是抽象类.

##### 6.2 super(subobj, self)

super函数是用来进行父类的初始化, 输入的是,该类的抽象(subobj)和实例(self)

```python
class FooChild(FooParent):
    def __init__(self):
        super(FooChild,self).__init__()    
        print ('Child')
```

##### 6.2 issubclass(sub, parent)

这里的两个参数都是抽象类.

```python
class Foo(object):
    pass
class Bar(Foo):
    pass
issubclass(Bar, Foo)
```



#### 7. 反射

**定义：主要指程序可以访问、监测和修改它本身状态或行为的一种能力（自省）。**

**python面向对象中的反射：通过字符串的形式操作对象相关的属性。python中的一切事物都是对象（都可以使用反射）, 包括函数, 变量,类**

##### 7.1 dir(object)

获取对象所有属性值. python中的一切事物都是对象（都可以使用反射）, 包括函数, 变量,类

```python
class Foo(object):
    staticField = "old boy"
    def __init__(self):
        self.name = 'wupeiqi'

a = Foo()
print dir(Foo)
>>> ['__class__', '__delattr__', '__dict__', '__doc__', '__format__', '__getattribute__', '__hash__', '__init__', '__module__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', 'staticField']
print dir(a)
>>> ['__class__', '__delattr__', '__dict__', '__doc__', '__format__', '__getattribute__', '__hash__', '__init__', '__module__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', 'name', 'staticField']
```

从上面可以看出,  类的实例 a 只比 类的抽象对象多一个 name 属性, 这个就是 __init\_\_ 函数实例化带来的结果. 其中, \_\_dict\_\_ 保存的是一些属性的值(不是全部). 并且抽象类和实例类中的 \_\_dict\_\_ 的内容是不同的.

##### 7.2 getattr

获取属性, 属性以字符串形式存在.

```python
class Foo(object):
    staticField = "old boy"
    def __init__(self):
        self.name = 'wupeiqi'
    def func(self):
        return 'func'
    @staticmethod
    def bar():
        return 'bar'
print getattr(Foo, 'staticField')
>>> old boy
print getattr(Foo, 'func') # 由于这是一个抽象类，所有其中的方法没有地址．
>>> <unbound method Foo.func>
print getattr(Foo, 'bar') # 虽然这是个抽象方法, 但是其中的静态方法是有地址的.
>>> <function bar at 0x7f31683e3758>
```

##### 7.3 setattr,hasattr, delattr

设置属性,判断是否拥有属性, 删除属性

##### 7.4 del, print, [.]的引用

这三个操作分别针对着object的三个内置方法:

```python
class Foo:
    def __getattr__(self, item): # 对应着 print 操作
        print(self.__dict__["item"])
    def __setattr__(self, key, value): # 对应着[A.b=c]操作
      # self.key=value #这就无限递归了,因为self.key=value本身就调用了self的__setattr__函数
        self.__dict__[key]=value 
    def __delattr__(self, item): # 对应者del操作.
        self.__dict__.pop(item)
```



#### 8. with

python 中什么好的一个机制, 可以像C++语言中的初始化函数和析构函数一样使用.

with语句需要支持`上下文管理协议的对象`, 上下文管理协议包含\_\_enter\_\_ 和\_\_exit\_\_ 两个方法. with语句建立运行时上下文需要通过这两个方法执行`进入和退出`操作.

可以利用这个去进行释放类内内存等等的操作.



#### 9. yield

yield可以用来生成迭代器. 在一般使用时,如果需要返回多个对象, 我一般多使用列表append, 然后返回列表. 

接受到这个列表后, 再通过 for in 来提取, 这样显得很笨拙.

用yield就可以解决这个问题.



#### 10. 动态类型

这个非常重要, 之后要养成类型制定的习惯.

python是一个强类型的动态类型. 也就是说, 不同类型直接是无法操作的, 说明是强类型. 但是一个变量可以通过简单的赋值操作进行类型转换, 这个就是动态类型.

python作为动态类型语言的优势也是很明显的, 天然支持了多态, 不需要设计复杂的代码去实现一种逻辑处理在多种类型下的应用. 但是在一些基础函数中, 这个反而会成为一种缺点, 尤其是读源码的时候, 无法得知变量类型. 带来很大不便.

因此, 可以利用 python3 中的 \_annotation\_ 来对类型进行注释.

但是也可以通过 inspect 模块, 自己定义一个强类型的函数. 其中也利用到了  \_annotation\_ 机制, 因为, inspect 用到了函数自带的 \_annotation\_ 属性, 这个属性就是python3 新加的机制的来源.详见[博客](https://www.cnblogs.com/boostable/p/python_annotations.html)



#### 11. @

行列式的内积符号.
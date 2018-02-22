## programing tricks

-   **enumerate:**

    ```python
    list1 = ["这", "是", "一个", "测试"]
    for index, item in enumerate(list1):
        print index, item
    >>>
    0 这
    1 是
    2 一个
    3 测试
    ```

- **%timeit**

    在ipython中, %timeit后跟一个语句可以对此语句的执行时间进行评价

    在程序中也可以通过import timeit来实现：

    ```python
    import timeit
    timeit.timeit('"-".join(str(n) for n in range(100))',number=10000)
    ```

    ​

-   库的安装目录

    - 使用pip安装的库是在 ~/.local/lib/python3.5/site-packages
    - python3.5 自带的库是在 /usr/lib/python3/dist-packages |　/usr/lib/python3.5/

-   切换python的版本

    update-alternatives --config python

-   python3的关于迭代器的设置

    python3相比于python2一个很大的改进是, 对于各个数据结构的返回结构的改进,由list转换为iterator. 

    有,metaclass，raise、map、filter以及dict的items/keys/values

    > **filter()** 函数用于过滤序列，过滤掉不符合条件的元素，返回由符合条件元素组成的新列表。

-   **list的深拷贝与浅拷贝**

    ```python
    a = [1,2,3,4,5]
    del a  # 直接删除这个变量
    del a[:] # 删除全部的内容,但保存这个变量
    del a[1] # 删除第一个元素
    # 浅拷贝
    list = a
    list is a # True, 一般的拷贝都是浅拷贝
    # 深拷贝
    list = a[:]
    list is a # False
    ```

-   **利用dict去实现switch case功能**

    ```python

    def dispatch_dict(operator, x, y):
        return {
            'add': lambda: x + y,
            'sub': lambda: x - y,
            'mul': lambda: x * y,
            'div': lambda: x / y,
        }.get(operator, lambda: None)()

    print(dispatch_dict("add",2,3))
    ```

-   **func在python是作为class存在的**

    可以向处理对象一样处理函数.

    ```python
    funcs = [func1, func2, func3]
    for f in funcs:
        f(para)
    ```

-   **= 和 is的区别**

    'is' : 两个对象在内存上的同一块位置,也就是指向同一个对象.(但是两个是不同的指针,因此还是存在不一样的地方)

    '==' : 两个数据对象具有相同的内容,但不要求其指向同一对象.

-   **ele in set & ele in dict的时间复杂度是 O(1)**

    因为使用的是has索引.

    下面的是dict中每个值的结构:

    ```c
    typedef struct {
    Py_ssize_t me_hash; // hash字符, 可以理解为空间换时间
    PyObject *me_key; 
    PyObject *me_value;
    } PyDictEntry;
    ```

    下面是一个dict的整体结构:

    ```c
    struct _dictobject {
    PyObject_HEAD
    Py_ssize_t ma_fill; // 交代清楚曾经有效元素的个数（用来计算加载率）。
    Py_ssize_t ma_used; // 交代出其有效元素的个数
    // 这两个变量是为了解决开放寻址中的再寻址造成的空位问题.
    Py_ssize_t ma_mask;　// 牵扯到hash中的散列函数
    PyDictEntry *ma_table;// ma_table始终指向有效散列空间的开始位置
    PyDictEntry *(*ma_lookup)(PyDictObject *mp, PyObject *key, long hash);
    // ma_lookup是这个寻址函数的抽象表示，通过将函数作为一个值赋予一个变量，再对这个变量取指针，可以直接应用到函数上，例如：
    // f = func
    // f(para) | (*pf)(para) :直接用于程序中
    // Void fuc(int nValue,int (*pf)(int,int));  
    // 这个函数表明，　想ｆｕｃ函数中传入一个输入为(int,int), 输出为ｉｎｔ的函数．
    PyDictEntry ma_smalltable[PyDict_MINSIZE];
    // ma_smalltable，python一向的有限空间换时间，一个小池子来应付大多数的小dict（不超过PyDict_MINSIZE）;
    };
    // ma_lookup:则是一次探测与二次探测函数的实现。
    ```

    dict中的hash寻址方式是开放定址,

-   **交换两个数**

     a,b = b,a

- **各种数据结构的指针使用方法**

    ```python
    # Function argument unpacking

    def myfunc(x, y, z):
        print(x, y, z)
        
    list_vec = [1,0,1]
    tuple_vec = (1, 0, 1)
    dict_vec = {'x': 1, 'y': 0, 'z': 1}
    >>> myfunc(*tuple_vec)
    1, 0, 1
    >>> myfunc(**dict_vec)
    1, 0, 1
    >>> myfunc(*dict_vec)
    x, y, z
    >>> myfunc(*list_vec)
    1, 0, 1
    ```

    ​

- **数据类型制定**

    这是python3中新引进的机制,  可以限制函数的参数类型, 且类型除了基础类型外, 也可以是自己定义的数据类型.
    ```python
    def f(number: str, eggs: str = 'eggs') -> str:
        print("Annotations:", f.__annotations__)
        print("I have eat {0} {1}".format(number, eggs))
        return number + ' and ' + eggs
    f("100")
    print("----------------------")
    f(100)
    ```

    执行之后, 发现第一个是可以执行的, 第二个会出现类型错误.


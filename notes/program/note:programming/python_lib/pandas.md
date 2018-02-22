### pandas



### 1. data structures

#### 1.1 series

- **基本结构**

  这个和Python自带的list和numpy中的array有些相似.

  虽然相似不过也有不同的地方:每个值都自带一个索引.

  ```python
  > s = pandas.Series(['zhaoxin',1996])
  > s
  > 
  0    zhaoxin
  1       1996
  dtype: object
  ```

  通过上面这个例子我们可以知道两点:

  1> 默认索引为从0开始的数字

  2> 一个列表中可以存在不同的类型

  3> 每个series有自己的数据类型标签 dtype

- **定义方法**

  - 最基本的定义方法

    ```python
    s = pandas.Series(['zhaoxin',1996])
    ```


  - 还可以定义Index名称

    ```python
    > s = pandas.Series(['zhaoxin',1996],index=['name','birth-year'])
    > 
    name          zhaoxin
    birth-year       1996
    dtype: object
    ```

  - 从python中的词典数据类型转化:

    ```python
    > d = {'name':'zhaoxin','birth-year':1996}
    > s = pandas.Series(d)
    ```

- **特殊性质**

  - **自动对齐**

    这个功能可以自动根据更新过后的index列表来排列之前的series,并且,如果缺少的index,series也将会删去其值,若是增加了新的index,则赋值给其Nan.

    ```python
    > s = pandas.Series(['zhaoxin',1996,10,21],index=['name','birth-year','birth-month','birth-day'])
    > s2 = pandas.Series(s,index=['birth-year','birth-month','birth-day','schcool'])
    > 
    birth-year     1996
    birth-month      10
    birth-day        21
    schcool         NaN
    dtype: object
    ```

  - **重新定义index**

    ```python
    > s = pandas.Series(['zhaoxin',1996,10,21],index=['name','birth-year','birth-month','birth-day'])
    >
    name           zhaoxin
    birth-year        1996
    birth-month         10
    birth-day           21
    dtype: object
    > s.index=['birth-year','birth-month','birth-day','schcool']
    > 
    birth-year     zhaoxin
    birth-month       1996
    birth-day           10
    schcool             21
    dtype: object
    ```

    可以发现,使用 s.index=... 改变index的方法和 pandas.Series(s,index=[...]) 的效果是完全不一样的.

    ​

- **使用方法**

  - 判断是否为空

    ```python
    > s = pandas.Series(['zhaoxin',1996,10,21],index=['name','birth-year','birth-month','birth-day'])
    > s2 = pandas.Series(s,index=['birth-year','birth-month','birth-day','schcool'])
    > s2.isnull() # pandas.isnull(s2) also works
    > 
    birth-year     False
    birth-month    False
    birth-day      False
    schcool         True
    dtype: bool
    ```

  - 调取某index下的值

    和python中字典的方法一样

  - 还可以进行运算

    前提是,一个series中的值全为一致的数据结构

  - 按照条件抽取

    ```python
    > example = pandas.Series([1,10,100,1000])
    > example[example>10]
    > 
    2     100
    3    1000
    dtype: int64
    ```

    ​

#### 1.2 DataFrame

- **基本结构**

  ```python
  > from pandas import DataFrame
  > data = {"name":['google','baidu','yahoo'],"marks":[100,200,300],"price":[1,2,3]}
  > f1 = DataFrame(data)
  > 
     marks    name  price
  0    100  google      1
  1    200   baidu      2
  2    300   yahoo      3
  ```

  说白了就是多个字典的容器.


- **定义方法**

  - 使用dict定义

    如上

  - **字典套字典**

    这样给每行也能加上index

    ```python
    > newdata = {'lang':{'first':'python','second':'java'},'price':{'first':5000,'second':2000}}
    > f4 = DataFrame(newdata)
    > 
    　　　		lang 　　 price
    first　　  python 　 5000
    second    java 　　 2000
    ```

- **一些方法**

  - **自定义行index**

    ```python
    > data = {"name":['google','baidu','yahoo'],"marks":[100,200,300],"price":[1,2,3]}
    > f3 = DataFrame(data,columns=['name','marks','price'],index=['a','b','c'])
    >
         name  marks  price
    a  google    100      1
    b   baidu    200      2
    c   yahoo    300      3
    ```

  - **改变列的顺序**

    ```python
     > f2 = DataFrame(data,columns=['name','price','marks'])
     > 
         name  price  marks
    0  google      1    100
    1   baidu      2    200
    2   yahoo      3    300
    ```

  - **可以根据列index提取某一列的index**

    ```python
    > f3['price']
    >
    a    1
    b    2
    c    3
    Name: price, dtype: int64
    ```

  - **可以根据两个index,抽取某一个值**

    不演示了



### 2. Functions

#### 2.1 用pandas中的DataFrame时选取行或列

- dataframe.iloc[i:j]:

  选取从dataframe中从第i行到第j行的数据形成新的dataframe

- dataframe['list_index']:

  返回的是一个series

  而 dataframe[['list_index']]: 返回的是一个小的dataframe

- ​
### Big file processing methods with python



#### 1. Read

```python
input_file = open(infile_name,'r')
while(1):
    for i in range(100):
        print input_file.readline()
    input = raw_input('Enter STOP to stop')
    if input == 'STOP':
        break
```

#### 2. **Break large file into small units**

这个需要将整个过程分为几步来搞:

- **Break large file into small units**

  ```python
  with open(input_file) as f:
      for line in f:
          process(line)
  ```

  在这个里面,在一个时刻内,RAM中只存有一个LINE的数据.

#### 3. Process the small units in parallel 

首先介绍python的并行机制:

- 基本结构:

  ```python
  def func1(param1):
      pass
  def func2(param2):
      pass
  pool = multiprocessing.Pool(3)
  pool.apply(func1,param1)
  pool.apply(func2,param2)
  ```

- apply_async:

  ```python
  def func1(param1):
      pass
  def func2(param2):
      pass
  pool = multiprocessing.Pool(3)
  pool.apply_async(func1,param1)
  pool.apply_async(func2,param2)
  ```

  > 1. 异步非阻塞的意思是:发出请求后返回,有结果时程序会收到通知.
  > 2. 异步阻塞的意思是:发出请求后等待(阻塞),有结果时程序会收到通知 (没有意义)
  > 3. 同步非阻塞的意思是:发出请求后返回,程序本身通过轮询的方式尝试获取结果
  > 4. 同步阻塞的意思是:发出请求后等待(阻塞),有结果后直接取走.
  >
  > **通过上面这个叙述,总结同步异步的区别以及阻塞非阻塞的区别**
  >
  > 1. 同步异步是指取得结果的方式,同步是程序本身去询问操作系统数据是否准备好,而异步是由操作系统通知该程序.
  > 2. 阻塞非阻塞是指程序在发出请求后是否返回.

  ​

  这个apply_async的功能是,打开了异歩非阻塞功能,当进程阻塞时,他将跳过这个程序。在异步非阻塞的情况下我们知道主程序会忽略子进程的进行,直接结束.这样也将导致子进程无法执行,因此,需要进行下面的改进.

- pool.join:

  这个要求主程序在完成子进程的处理后才会退出.

  ```pyhton
  def func1(param1):
      pass
  def func2(param2):
      pass
  pool = multiprocessing.Pool(3)
  pool.apply_async(func1,param1)
  pool.apply_async(func2,param2)
  pool.close()
  pool.join()
  ```

#### 4. Save memory 

除了运行时间之外,运行所需内存也是极为稀缺的.

如果我们选择每次向process函数中,直接传入要处理的问题,那么根据apply_async的特性,程序很有可能将所有的数据加载到内存中.因此,我们需要做的是,在不同的subprocessing中传入这部分数据所在的位置,而不是其数据内容.

这里使用的是文件操作中的:seek以及tell函数.

```python
def process(Byte_location):
    with open(file_name) as f:
        f.seek(Byte_location)
        for i in range(lines_size):
            line_data = f.readline() + '\n'
            all_data += line_data
		process(all_data)
        return f.tell()
```


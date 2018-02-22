### word2vec program summary



#### 1. Frame

主程序一共可以分为五个部分:

- 参数的设置.

- 生成数据迭代器

  包含batch_size, repeat, shuffle 等超参数.

  原始数据的处理, 以及生成数据迭代器. 这也是在word2vec中最重要的一个部分. 

- 模型的搭建(包含, 层数等等模型相关超参数)

- 配置优化器 (训练算法及下降速率等等优化相关参数)

- 配置更新器 (将数据迭代器和优化器连接起来.)

- 配置训练器 (设置stop_trigger超参数,也就是迭代次数的参数)



#### 2. Hyper Parameter

这里的超参数设置主要是指如何通过外界参数接受. 

这里主要通过 argparse 模块来实现. 

设置方法也很简单. 分为两步.

- 实例化参数的解析器:

  ```python
  paser = argparse.ArgumentParser(description="...")
  ```

- 设置可选参数,初始数据,以及数据类型.

  ```python
  parser.add_argument('--parament','-b',type=type, default=init, help="")
  ```

- 传递给一个数据类

  ```python
  args = parser.parse_args()
  ```

之后就可以通过调用 args.parameter 的方式, 去调用上面的参数. 非常的简单方便.



#### 3. Data processing

这个也是最费时的一步操作, 这里详细讲, 以及将代码可复用化.





#### 4. Model

模型相对简单.这里着重讲几个结构.下面是模型的类.

```python
class SkipModel(chainer.link.Chain):
    def __init__(self, dataMes, embed_size, lossfun=
                 negative_sampling.NegativeSampling):
        super(SkipModel,self).__init__()
        with self.init_scope():
            self.wordCounts = ... # 每个词汇的词频信息.是个列表
            self.embedings = L.EmbedID(
                dataMes.vocabSize, embed_size, initialW=I.Uniform
                (1. / embed_size))
            self.lossfun = negative_sampling.NegativeSampling
            	(embed_size,self.wordCounts, sample_size=5)
    def __call__(self, xwords, pwords):
        shape = self.embedings(pwords).shape
        pwords_embed =	self.embedings(pwords).
        	reshape(shape[0]*shape[1],shape[2])
        loss = self.lossfun(pwords_embed,xwords,reduce='sum')
        reporter.report({'loss': loss}, self)
        return loss
```

首先, 这个模型是一个简单神经网络, 就是三层的那种, 使用的损失函数是负采样softmax.输入是长度为词库数量N的one-hot矩阵. 中间层决定着词向量的长度M. 输出和输入一样长度的softmax值.

首先是通过

```python
self.embedings = L.EmbedID(
                dataMes.vocabSize, embed_size, initialW=I.Uniform
                (1. / embed_size))
```

初始化一个 N*M 的矩阵, 使用的 uniform 随机初始化.接下来:

```python
self.lossfun = negative_sampling.NegativeSampling
            	(embed_size,self.wordCounts, sample_size=5)
```

self.lossfunc 这里采用的是negative_sampling.NegativeSampling类, 用于从中间层到输出层的参数初始化以及其他的基础设置. 注意 ,这里的初始化只是赋予它一些基本属性, 包括中间层长度以及负采样的个数, 以及采样需要的词频信息.

接下来是call, 注意这里的xwords和pwords分别是 batchsize长度 的词索引. 

pwords.shape = xwords.shape = (batchsize,)

再接下来执行负采样损失函数计算.

最后,reporter的那里是用于输出loss到外界, 详细见Validation部分

#### 5. Optimizer, Updater, Trainer





#### 6. Validation & Reporter

##### 6.1 Validation setting

- **验证集数据**

  验证集数据采用的是, 从原文本中分割出来的一部分, 过程如下:

  ```python
  # 首先从数据分析器中获取格式化,以及分割之后的数据
  train, val = analysis(val_rate=0.2, window=2) 
  # 传入数据迭代类获取数据迭代器
  val_iter   = dataIterator.dataIterator(analysis, val, batch_size=128, repeat=False, shuffle=False)
  ```


- **加入trainer.extend**

  trainer.extend 是一个非常重要的类.

  - 首先, 他是属于trainer, 意味者, extend中的方法都会随着训练周期的运行. 

    这个通过定义可以推测出:

    ```python
    # trigger代表着执行这个extension的周期. priority代表着执行顺序(因为有很多extension)
    def extend(self, extension, name=None, trigger=None, priority=None,
                   **kwargs):
    ```

  - 其次, extend(即, 扩展) 意味着, 其中的方法与主要的模型训练无直接关系, 是辅助训练的过程.

  代码如下:

  ```python
  # extensions.Evaluator :Trainer extension to evaluate models on a validation set.
  trainer.extend(extensions.Evaluator(val_iter, model, converter=convert))
  ```

##### 6.2 Reporter

- **name 机制**

  首先要搞明白chainer中的 name 机制. 

  这些为了测试进行的向外部报告的机制的实现是 reporter. 

  reporter中有几个重要的东西:

  - **scope(self, observation) 和 全局列表self._reporters**

    这是一个进入scope的机制, 因为在一次train中, 我们可能有运行model实例, 或者运行不同model的实例,或者同一模型给不同数据(train和validation)的可能性, 一般的做法我们可能是,为每个模型分配一個reporter, 但是chainer为了在用户层面, 尽可能的节省用户的操作, 这里才用了scope进行了reporter分配. 

    分配的方法是:

    - scope(self, observation):

      ```python
      def scope(self, observation):
          old = self.observation
          self.observation = observation
          self.__enter__()
          yield
          self.__exit__(None, None, None)
          self.observation = old
      def __enter__(self):
          _reporters.append(self)
      def __exit__(self, exc_type, exc_value, traceback):
          _reporters.pop()
      ```

      这个在 trainer.run() 和 evaluator.evalute() 里面都会被实行.

    - _reporters:

      reporter.py 中的一个全局列表 _reporters . 也就是说, 一次程序运行中, 可能会使用到多个reporter, 但是都会被以先进先出的方式,出存在 _reporters中. 使用方法是:

      ```python
      def get_current_reporter():
          """Returns the current reporter object."""
          return _reporters[-1]
      ```

      例如在这个程序中, 我们会在每次epoch完成之后, 利用训练的模型去进行一次测试, 这个时候, 我们使用的是, train.extend(extend.extension()) , 这个里面会定义一个reporter ,虽然模型是一样的,参数是一样的, 但是由于是不同的数据, 所以需要新建一个reporter. 

      然后, 在重新运行这个evaluator里面, 我们需要运行函数 evaluator.evalute(). 这个函数和 trainer.run() 相似, 都运行了下面的部分:

      ```python
      with reporter_module.report_scope(observation):
        	...
      # trainer.run() main training loop
      try:
          while not stop_trigger(self):
              self.observation = {}
              with reporter.scope(self.observation):
                  update()
                  for name, entry in extensions:
                      if entry.trigger(self):
                          entry.extension(self)
      ```

      当执行scope() 中的 __enter\_\_ 的时候, 就会进入一个新的reporter scope, 也就意味着上面的 _reporters 的长度将会由1变为2, 因为增加了evaluator的reporter. 推出之后, 就会变回原来的长度

  - **reporter.report 和 Reporter.report**

    前者是reporter.py的全局函数 report()

    后者是 class Reporter 的方法, report()

    前者调用了后者

    ```python
    # observer: observer在数据意义上代表的是任意一个类. 这个类在train的过程中有着一个名字, 这个名字代表了他的使用范围. 就是对应着 "validation/main/loss"里的"validation/main" 字段. 
    def report(values, observer=None):
        if _reporters:
            current = _reporters[-1]
            current.report(values, observer)
    ```

    而在后者中,有如下代码:

    ```python
    def report(self, values, observer=None):
        observer_name = self._observer_names[observer_id]
        for key, value in six.iteritems(values):
            name = '%s/%s' % (observer_name, key)
            self.observation[name] = value
    ```

    这里就很明显了, 将observer+"/"+key作为新的key, 赋给了self.observation.

    这里的observer_name是observer的名称,  也就是" valiadation/main/loss" 中的 "" valiadation/main"的部分, 由reporter的变量 self._observer_names 维护.

    reporter下的_observer_names变量就是一个储存 

    ​				{类的地址:该类的scope名称}

    的数据结构.下面会细讲.

  - **self.observation 和 self._observer_names**

    每个reporter都会有一对这样的字典, 

    self.observation 字典储存该reporter中可被外部审查的变量的 {名称:变量数据内容} 对.

    self._observer_names 字典储存一个 {类的地址:该类的scope} 对.

    其中定义的方法是在自己model里面添加可审查属性:

    ```python
    reporter.report({'loss': loss}, self)
    ```

    这个里面的report函数处理的对象就是全局变量, _reporters, 见上面.

    而将, 变量赋予变量名的方式是: reporter.add_observer

    ```python
    def add_observer(self, name, observer):
        self._observer_names[id(observer)] = name
    ```

    这里的 id(observer) 是python的一个内置函数, 取的是,参数变量的地址, 因此是唯一的, 不可能发生冲突的.  

- **"main/loss" 的来源**

  首先我们先来确定, "main"字段的来源. 

  ```python
  # trainer.__init__():初始化了一个reporter. 可以看到,这个里面已经调用了一次, add_observer, 也就是说, 这个时候已经产生了一个字段, 就是 "main", 并且将这个字段储存到trainer自己的私有变量 self.reporter中. 
  reporter = reporter_module.Reporter()
  for name, optimizer in six.iteritems(updater.get_all_optimizers()):
      reporter.add_observer(name, optimizer.target)
      reporter.add_observers(
          name, optimizer.target.namedlinks(skipself=True))
  self.reporter = reporter
  # 而这个main的字段产生于updater.get_all_optimizers,下面查看updater的源码:
  def __init__:
      if not isinstance(optimizer, dict):
          optimizer = {'main': optimizer}
          self._optimizers = optimizer
  def get_all_optimizers(self):
      return dict(self._optimizers)
  # 这样的话,就知道了main的来源

  # trainer.run():接下来进入run的main loop中, 发现这里第一次用reporter进行了scope限定. 这样的话, reporter.py下的全局变量 _reporters 中就有了一个reporter实例, 且这个实例的 _observer_names 变量中存储的数据为 {'main':optimizer.target}. optimizer.target的本质是一个link对象的地址.
  reporter = self.reporter
  stop_trigger = self.stop_trigger
  try:
      while not stop_trigger(self):
          self.observation = {}
          with reporter.scope(self.observation):
              update()
              for name, entry in extensions:
                  if entry.trigger(self):
                      entry.extension(self)
  ```

  接下来看如何把 'loss' 加到 'main' 上面, 组成 'main/loss' 字段.

  这个在将 **reporter.report 和 Reporter.report** 的时候已经进行了详细说明, 通过reporter自身的report方法, 向self.observation 中加入了 loss字段.


- **"Validation/main/loss" 的来源**

  这个是最难的一个部分. 

  首先看代码:

  ```python
  trainer.extend(extensions.Evaluator(val_iter, model, converter=convert))
  trainer.extend(extensions.PrintReport(['epoch', 'main/loss', 'validation/main/loss']))
  trainer.extend(extensions.PlotReport(['main/loss', 'validation/main/loss'],'epoch', file_name='loss.png'))
  trainer.extend(extensions.PlotReport(['main/accuracy', 'validation/main/accuracy'],'epoch', file_name='accuracy.png'))
  ```

  那么我们大概知道,这段代码的主要意思就是向trainer中加入几个extension函数, 使其在main loop时可以执行, 那么main loop是怎么执行这段代码呢?

  ```python
  # trainer.run main training loop
  while not stop_trigger(self):
      self.observation = {}
      with reporter.scope(self.observation):
          update()
          for name, entry in extensions:
              if entry.trigger(self):
                  entry.extension(self)
  ```

  在这里, 我们需要知道, while执行一次就代表迭代了一次, 也就是iteration多了一次. 这里最重要的是倒数第二行, 也就是说每次迭代完成后都要检测一次是否可以执行extension函数, 而检测的条件就是trigger-触发器, 触发器在前面也讲过.这个和上面的 extension中的输入参数"epoch"相对应.

  接下来进入, extension(self), 也就是每个extension的 __call\_\_ 函数. 

  再看 __call\_\_ 之前还是有必要看下初始化extension的过程. 以

  ```python
  trainer.extend(extensions.Evaluator(val_iter, model, converter=convert))
  ```

  为例,

  首先, 在trainer.extend()函数中, 

  ```python
  if name is None:
              name = getattr(extension, 'name', None)
              if name is None:
                  name = getattr(extension, 'default_name', None)
                  if name is None:
                      name = getattr(extension, '__name__', None)
                      if name is None:
                          raise TypeError('name is not given for the extension')
  self._extensions[modified_name] = _ExtensionEntry(
              extension, priority, trigger)
  ```

  这个里面, 我们看到, extend函数拼了命的要找到extension里面 name 属性. 然后将name赋给管理extension的数据结构.这个 name 属性就是每个extension必须拥有的一个属性. 

  现在看 extensions.Evaluator 和 extensions.PrintReport 这两个类的内容:

   extensions.Evaluator 

  ```python
  class Evaluator:
      trigger = 1, 'epoch'
      default_name = 'validation'
      priority = extension.PRIORITY_WRITER
  # 可以看到, 这个类具有三个内置属性.
  ```

  extensions.PrintReport

  ```python
  # 在这个类中并没有显式的定义他的name, 而是通过继承父类 <extension.Extension> 
  class PlotReport(extension.Extension):
  # 的方法, defalut_name, 来获取名称.
  @property
  def default_name(self):
      return type(self).__name__
  ```

  然后在, evaluator的__call\_\_里面 ,有这么一段:

  ```python
  def __call__(self, trainer=None):
      reporter = reporter_module.Reporter()
      if self.name is not None:
          prefix = self.name + '/'
      else:
          prefix = ''
      for name, target in six.iteritems(self._targets):
          reporter.add_observer(prefix + name, target)
          reporter.add_observers(prefix + name,
                                 target.namedlinks(skipself=True))

      with reporter:
          with configuration.using_config('train', False):
              result = self.evaluate()

      reporter_module.report(result)
      return result
  ```

  在这个里面可以看到,  里面有个 self.name ,这个就是我们上面讲过的 "validation" 字段. 接下来是, for name, target in six.iteritems(self._targets): 中的name. 首先看 self.\_targets

   self._targets 是在 __init\_\_ 中定义的, 包含了对模型的name定义, 源码是这样的.

  ```python
  if isinstance(target, link.Link):
      target = {'main': target}
  self._targets = target
  ```

  而这里的target就是传给evaluator函数的model, 在这里也就是 SkipModel.

  然后, target被放到这个新的reporter的  _observer_names 中去.

  接下来, 来到evaluate真正执行的函数. evaluator.evaluate()

  ```python
  def evaluate(self):
      summary = reporter_module.DictSummary()
      for batch in it:
          observation = {}
          with reporter_module.report_scope(observation):
              eval_func(*in_arrays) # 执行模型.
      return summary.compute_mean()
  ```

  在report_scope中, 想reporter中的 _reporter中再次加入一个新的reporter. 

  那么,在执行模型的过程中,report{loss} 再次被执行, 这次执行的对象是 新的reporter. 这个新的reporter的add_observers里面装得是 {SkipModel的地址 : "validation/main"}. 

  虽然 reporter 使用的都是同一个skipmodel, 但是其对应的 name 是不一样的. 然后, 在reporter的report函数中, loss被加到  "validation/main" 上, 成为  "validation/main/loss"
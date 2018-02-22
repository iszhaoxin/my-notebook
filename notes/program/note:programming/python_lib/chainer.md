## Chainer



### 1. Core concept

chainer 和其他框架最不一样的地方在于,这是一个动态的神经网络构建框架.

#### 1). define and run

其他的比如说tensorflow, 据我了解,是先构建一个完整的模型逻辑, 即建立起一个完整的计算图,之后不停的向其中添加数据,进行训练. 也就是说这些框架是先把路全部铺好之后才可以通行.

#### 2). define by run

而chain可以在前向的过程中建立网络模型.也就是说,chainer是边向前走边铺路.chainer记录的是计算的历史,而不是计算的逻辑.



### 2. Data structure Levels in Chainer

下面介绍chainer中数据结构的抽象等级,这里按照我的理解分为两个个等级.由低到高介绍.

chainer的模型是以等级架构构建起来的,就像文件系统中的文件路径与文件一样,第一个等级的variables , parameter and functions就是文件,下面的link和chain就是文件路径.

#### 1). **variables , parameter and functions**

- [**Variable**](https://docs.chainer.org/en/stable/reference/core/generated/chainer.Variable.html)

  是存储input,output,隐含单元输出值等等真实含有数据的数据结构.

  Variable中含有两种抽象数据.

  - 一种就是普通的数据数组,这里采用的是numpy的数据储存格式.
  - 还有一种是 VariableNode.用于存储计算图中的节点.我们知道计算图是一个图,是有结点,并且都是互相联结在一起的.因此这里的 VariableNode 如果是手动初始化的时候,这个variable实体就没有父节点. 而如果这个variable是通过functionNode计算出来的话,那么这个节点计算的时候就会自动调用creator_node.

  在这个class中,可以调用后向传递函数 y.backward() 通过这个函数,将对计算y的自变量variable计算其梯度.可以通过调用 x.grad 来查看梯度值.

- [**Parameter**](https://docs.chainer.org/en/stable/reference/core/generated/chainer.Parameter.html#chainer.Parameter)

  正如名字所示,这个就是储存映射过程中过的参数信息.就是我们训练神经网络想要去调整的那些东西.

  比如,我们在采用线性映射的函数是,会这样调用

  ```python
  y = L.linear(in_size,out_size)
  ```

  那么在这个里面,生成的 in_size*out_size 大小的矩阵,储存的就是parameter类型的.

  **下面注意了**:

  这里的初始化其实是建立了两个这么大的数组,一个储存的是数据本身,而另外一个存储的是该变量对应的梯度矩阵.初始化的时候,可以选择初始化方法.

  当我们在后面运行时,调用 backward()函数的时候,就会link一个梯度下降的method给梯度矩阵,去更新梯度矩阵.

  > addgrad() :将梯度数组累加到参数数据数组上,因为有可能有几个变量对该变量都有梯度影响.
  >
  > backward() : 运行后向传递
  >
  > initialize() : 初始化

- [**chainer.variable.VariableNode**](https://docs.chainer.org/en/stable/reference/core/generated/chainer.variable.VariableNode.html#chainer.variable.VariableNode)

  variable数据类型的子类,链接数据到一个计算图中的点上.

- **functions**

  首先,functions中的每个函数也是计算图中的一个基本组成部分,是链接variable node和variable node之间的operation node. 这里设计了计算图的相关概念,详见 [博客](http://www.deepideas.net/deep-learning-from-scratch-i-computational-graphs/). 简单来说就是计算图包括两个基本元素, operation node和 variable node ,其中,variable node可以把他的值输入到 operation node中, 而 operation node可以输出另外的一个variable node.

  - chainer.functions.activation  -> 激活函数,
  - chainer.functions.array -> 处理数组的基本函数
  - chainer.functions.connection -> 链接方式函数,有卷积,线性,GRU,RNN等等
  - chainer.functions.evaluation -> 评价函数
  - chainer.functions.loss -> 损失函数
  - chainer.functions.math -> 基本数学函数
  - chainer.functions.noise -> 模型加入噪声的函数 ,dropout或者加入高斯噪声
  - chainer.functions.normalization -> 归一化模型,batch_normalization等等
  - chainer.functions.pooling -> 用于卷积的pool方法
  - 其他的三个函数(感觉用不上)

  更多内容见源码 chainer/functions

  > 这里的function和下面的function并不一样,这里的function主要是一些深度网络中会用到的基本函数.有下面几个大类,类中包含很多函数的小类,例如 chainer.functions.activation  中含有 relu 类,这些小类下面有 forward_cpu,forward_gpu函数和backward函数:

  functions中的函数经常被chainer.Link中的对象调用

- [**Function**](https://docs.chainer.org/en/stable/reference/core/generated/chainer.Function)

  这个类其实和上面的functions在功能上,以及等级上是一样的,都是用于对一个Variable进行操作,并且输出一个Variable. 可以说,functions中是定义了很多function结构. function这个类是一个抽象结构,里面有可以自己定制的东西. 而functions是库给定义好的,具象化好的function的集合,也就是function**s**. 

  这个function是定义计算图中 operation node 的一个重要的功能类.其有三个并列的分支.其他的还有三个更具体的,详见链接.

  - chainer.Function

    这是一个old-style class,也就是之前用,现在不用的class.里面的功能全都搬到了FunctionNode里去.function主要就是实现了operation node的forward和backward功能.

    其中,function和functionNode的不同点有两个:

    - function.backward是对于数组直接操作的,没有对variable node进行操作,因此无法记录数据历史.
    - 输入不同,由于function.backward无法记录结果,因此每次都需要输入从最开始的全部的variable中的数据.而functionNode.backward就只需要前面的一个就好了.

  - chainer.FunctionAdapter

    Adapter class to wrap Function with FunctionNode.将Function转换为FunctionNode,并且在计算图中加上这一个operation

  - chainer.FunctionNode

    主要功能在上面已经介绍了,这里主要说几个具体的函数.

    - apply():把input的variable加到计算图上
    - backward():计算某个节点后向梯度
    - backward_accumulate():计算某个节点联合梯度值
    - forward_cpu():input为numpy.ndarray
    - forward_gpu():input为Cupy.ndarray

  ​


**这里面的函数会被更高一级的抽象数据结构调用,下面讲解更高一级的数据结构**



#### 2).  Link and Chain

##### 1>. Link

- **使用方法**

  Link简而言之就是包含一层input variable node(抽象,无具体数据) ,一层output variable node(抽象,无具体数据), 和一个 input->output的operation node 的基本结构. 我们在平时自己写网络的时候,也就只是将这几个因素简单拼接起来而已,并没有在设置一个专门的类去管理这样的结构,但是chainer给出了这样的结构.一个典型的Link的定义格式为:

  ```python
  class LinearLayer(chainer.Link):
      def __init__(self, n_in, n_out):
          # LinearLayer为下面定义变量的scope
          super(LinearLayer, self).__init__()
          with self.init_scope():
              self.W = chainer.Parameter(
                  initializers.Normal(), (n_out, n_in))
              self.b = chainer.Parameter(
                  initializers.Zero(), (n_out,))

      def __call__(self, x):
          return F.linear(x, self.W, self.b)
  ```

  - 首先通过继承类chainer.Link继承一些基本方法(这些基本方法接下来介绍).
  - 利用self.init_scope函数,建立一个针对于该Link的参数初始化范围,将里面的parameter注册到这个范围里面.
  - 利用chainer.Parameter初始化参数,chainer.Parameter定义的时候,可以选择初始化的方法.
  - 通过定义 \__call__ 函数,在实例化该Link类的时候,便可以执行 \_\_call\_\_ 里面的内容,在这里即调用F.linear类来返回一个functionNode.

- **函数介绍**

  - add_param(*name*, *shape=None*, *dtype=<class 'numpy.float32'>*, *initializer=None*):

    就是向其中这个Link类中加入新的parameter.可被 chainer.Parameter 代替. 

    > 注意,Link内的参数都是有自己的名字的.例如:
    >
    > ```python
    > link.add_param('W', shape=(5, 3))
    > ```

  - add_persistent(name,value):

    这里是指加一些永久变量,不会变化或者被动消失,多为一些Link的属性设置.

  - addgrads(link)

    把给定link的梯度参数矩阵加到这个link的梯度参数矩阵上

  - children():

    返回该link的子link的genertor,就是输出为该link输入的link.之所以输出的结构为genertor,是因为首先一个link可以连很多个子link,就像卷积网络中的卷积层一样,可以链接很多个过滤器.其次,genertor便于iterator.

  - cleargrads():

    清楚所有的梯度参数矩阵.

  - copy():

    把整个link结构返回.

  - copyparams(*link*):

    从参数的link中拷贝参数过来

  - disable_update(): enable_update():

    取消更新,使能更新,可用于dropout

  - init_scope():

    上面说过了.

  - links(): namedlinks():

    返回在这个等级下的所有link. 在此基础上返回的是(link,linkname)的对.

  - serialize():

    下面将serializer的时候再细讲.

  这一节的函数是讲的最详细的,接下来也不会讲这么细了,大概就是这个意思.

- **已经制定好的links**

  chainer给出了一些常用的link架构.详见 源代码 chainer/link

  给出了特别多种类的link,感觉,编程起来会特别容易.

##### 2>. Chain

**Chain的简介是:Composable link with object-like interface.**

- **理念**

  chain本质上就是link的组合object.他将几个已经搭建好的link结合起来,做成一个综合的object.我们经常将整个模型设计为chainer.chain的一个子类.或者有时候将网络模型的一部分作为chain的子类.

  每个chain同时也是一个link,因此可以通过组合很多chain来构成一个更大的chain.这样的话,我们就发现links和chains整体构建出了一个**等级架构**. 这个等级架构是一个**树状结构**.

  ------

  > 刚才在上面将link的时候,涉及到了init_scope这个函数,这个函数涉及到了一个初始化范围的概念,而这个概念是建立在整个网络结构是一个树状结构的基础上说的.解释如下:
  >
  > 可以将每个parameter理解为一个文件,而每个文件都有一个自己的路径,在操作系统中,这个文件路径对应的是系统中注册的文件夹地址,而在这里对应的是不同的link和chain.
  >
  > 比如说,一个网络模型具有如下结构:
  >
  > Allchain:
  >
  > ​	\-  chain1
  >
  > ​		\- link_1-1
  >
  > ​			\- parameter_W_1-1
  >
  > ​			\- parameter_B_1-1
  >
  > ​		\- link_1-2
  >
  > ​			\- parameter_W_1-2
  >
  > ​	\-  link2
  >
  > ​	\-  link3
  >
  > 那么在这个模型中,parameter_W_1-1所归属的范围有,
  >
  > link_1-1.scope    chain1.scope   Allchain.scope

  ------

  ​


- **应用示例**

  ```python
  import chainer
  import chainer.functions as F
  import chainer.links as L

  class MultiLayerPerceptron(chainer.Chain):

      def __init__(self, n_in, n_hidden, n_out):
          # MultilayerPerceptron为下面定义变量的scope
          super(MultilayerPerceptron, self).__init__()
          with self.init_scope():
              self.layer1 = L.Linear(n_in, n_hidden)
              self.layer2 = L.Linear(n_hidden, n_hidden)
              self.layer3 = L.Linear(n_hidden, n_out)

      def __call__(self, x):
          # Forward propagation
          h1 = F.relu(self.layer1(x))
          h2 = F.relu(self.layer2(h1))
          return self.layer3(h2)
  ```

  具体的函数和link一样.

##### 3>. ChainList

**Chain的简介是:Composable link with list-like interface.**

他的作用是,可以用来构建深层神经网络,将link储存在一个数组中,这样就可以通过访问数组的方式访问link,也可以迭代的去构建link,适合特别深的网络.



**!!!!!到目前为止,模型构建就结束了,下面开始训练的相关设置**



#### 3). optimizer

在完成了模型的相关搭建后,下面就要进行一些超参数以及优化算法,训练方法相关的设定.

下面大概看下optimizer的使用方法:

```python
model = MyChain()
optimizer = optimizers.SGD()
# The method setup() prepares for the optimization given a link.
optimizer.setup(model)
optimizer.add_hook(chainer.optimizer.WeightDecay(0.0005))

def lossfun(arg1, arg2):
     # calculate loss
     loss = F.sum(model(arg1 - arg2))
     return loss
arg1 = np.random.uniform(-1, 1, (2, 4)).astype('f')
arg2 = np.random.uniform(-1, 1, (2, 4)).astype('f')
optimizer.update(lossfun, chainer.Variable(arg1), chainer.Variable(arg2))
```

看一些具体的类:

##### 1>. chain.optimizer

- **简介**

  这个类提供了所有优化方法的基本特征.他会优化目标link的参数.目标 link 可以通过setup()函数注册到optimizer object.

  每一个optimizer的实现(也就是写一个新的这个类),都需要定义为optimizer的子类,并且必须重载 update()方法.

  这个类的基本使用方法很简单,就是上面的那些:

  - 设置一个基本的optimizer方法 optimizer = optimizers.SGD()


  - 第一步是通过setup注册link到一个optimizer. optimizer.setup(model)
  - 第二步是通过update更新参数数据. optimizer.update(lossfun, chainer.Variable(arg1), chainer.Variable(arg2))

- 如果一个optimizer使用的是一阶方法的话,应该继承 chainer.GradientMethod class． 

- 的optimizer 可以通过add_hook()的方法给optimizer定制一些具体的信息.例如是否采用gradient clipping等等.

##### 2>. chainer.updateRule

这个类是对每个variable进行操作的,应该也可以直接对chain进行操作和设定.

#### 4). Trainer






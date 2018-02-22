## Cross-lingual Knowledge Graph Alignment via Graph Convolutional Networks



### 1. Introduction 

#### 1.1 要解决的问题

Cross-lingual KG alignment

1. 现在存在各种各样的拥有多语言的知识图谱, 而不同语种的知识库之间存在着共同实体
2. 这些跨语种的共同实体的存在对于那些研究多语种关系的研究非常重要.
3. 但是不是所有的共同实体都被标注了. 
4. 因此, 有了这个任务

#### 1.2 要解决的小问题

##### 1) 主流方法

条件 : 两个KG, 以及一个 pre-align entities set

大概有四个先行研究

1. JE : joint learning of a connected graph by seed entities between two different graphs

   ![](./pictures/1)

2. MTransE : 分别对两个知识图谱进行 embedding. 然后再用 Alignment 进行align.

   - Embedding Sperately

     ![](./pictures/2)

   - Alignment Model

     三种方法:

     - Distance-based :

       ![](./pictures/3)

     - Translation Vectors:

       ![](./pictures/4)

     - Linear Transformations

       ![](./pictures/5)

   - 组合:

     ![](./pictures/6)

   ​

3. JAPE : 加入了 attribute embedding 

4. ITransE : 利用以往的预测去增强训练集. 

##### 2) 存在的问题点

1. 需要设置超参数去平衡 embedding 和 alignment 的 loss 的比例.
2. 有些使用了 Relation Alignment 的信息. 
3. 有些不能使用实体的属性信息.



### 2. Methods

#### 2.1 GCN 的使用

没有使用 TransE 去预训练向量. 而是使用专门处理图结构的方法, GCN.

![](./pictures/7)

并且是属于 joint 模型. 

##### 2.2 Attribute information

![](./pictures/8)

![](./pictures/9)
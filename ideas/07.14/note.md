### 利用graph network去进行关系预测



利用Graph network的理由是想利用Latent Feature和Graph feature两个信息. 

- 首先为什么要用Graph feature?

  利用Latent Feature的模型都假设了Entity Embedding 含有其一些信息, 然后Relation Embedding 会根据两个Entity Embedding去判断两个Entity之间有没有该Relation.

  上面的方法只能编码一些全局特征, 例如Entity是否具有人的信息这种感觉, 因为Knowledge Graph中的实体是非常具体的. 

  但是对于局部的信息, 比如, 张三和李四是夫妻, 这个事情就无法用这种全局信息去表示, 首先即使Entity Embedding中有表示这个人是否婚嫁的信息, 但是是无法判断他和谁是夫妻的. 在这种情况下, 就要用路径模式匹配去做出判断, 例如, 他俩与一个人同为亲子关系. 而这个就要用到Graph feature.

- 如何利用 Graph feature?

  我的想法是:

  - 先将实体进行抽象化.
  - 利用Graph network进行信息整合.

  理由已经做法是

  对于上面的那个例子, 我们需要的信息是, 两人拥有同一个孩子小明, 在Knowledge Graph中的表示就是, 这两个人的node同时连向一个node, 其关系为亲子关系. 但是如果要利用Graph network去整合孩子的信息进入这对夫妻的node的话, 会引入噪音, 这个噪音是孩子的个人属性. 也就是说, 我们只想引进这个孩子作为人的相关属性, 因此需要先将这个孩子抽象到人的层次.  

  这里又有一个问题, 我们怎么知道应该将子node抽象到什么地步呢? 答 : 应该是使用关系向量去约束. 现在将上面问题进行模型化:

  设 $e_a, e_b$ 为上面的两个需要预测关系的nodes. $E_a, E_b$ 为他们的Entity Embedding. 

  此时, 设 $e_a, e_b$ 同时连向的节点有 $N$ 个, 设其组成集合 $e_N$ .

  对于每个 $e_i\in e_N$, $e_a, e_b$, 都有 triple nodes 信息元 $T_{abi}$: 

  ​	$T_{abi}=\{(e_a,r_{ai},e_i),(e_i,r_{ia},e_a),(e_b,r_{bi},e_i),(e_i,r_{ib},e_b),(e_a,r_{ab},e_b),(e_b,r_{ba},e_a)\}$

  为了一般化, 这里假设的是, 每两个node之间可能有多个关系. 对于上面 $r$ 不存在的情况, 直接忽视其所在元组就好.

  我们对于每个含有 $e_i$ 的元祖, 可以通过 $f(W_{r}E_i)$ 来对 $e_i$ 所要传递给 $e_a, e_b$ 的信息进行约束. 也就是说, 若 $W_r$ 为 [亲子关系] 的Relation Embedding, $E_i$ 为 [小明] 的Entity Embedding. 那么通过 $f(W_{r}E_i)$ 可以屏蔽掉 [小明] 的其他非人信息, 比如 [小明爱吃菜], [小明生活在中国] 等等的信息.

  整合后的信息可以直接加到 $E_a, E_b$ 上, 之后再利用 张量分解法 去判断 $e_a, e_b$ 是否含有关系 $r$ .

- **问题**

  上面的方法很有可能已经被做了, 做好在此之上进行改进的准备, 比如说利用 order embedding 或者 变分推断方法.

  ​
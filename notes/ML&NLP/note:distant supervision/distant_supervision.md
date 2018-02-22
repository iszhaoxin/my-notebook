### Distant supervision



用于关系提取的一项数据收集技术

An alternative approach to generating training data is **distant supervision**.
In distant supervision, we make use of an already existing database, such as [Freebase](http://www.freebase.com/) or a domain-specific database, to collect examples for the relation we want to extract. We then use these examples to automatically generate our training data. For example, Freebase contains the fact that Barack Obama and Michelle Obama are married. We take this fact, and then label each pair of "Barack Obama" and "Michelle Obama" that appear in the same sentence as a positive example for our marriage relation. This way we can easily generate a large amount of (possibly noisy) training data. Applying distant supervision to get positive examples for a particular relation is easy, but [generating negative examples](http://deepdive.stanford.edu/generating_negative_examples) is more of an art than a science.

Freebase是一个类似于维基百科一样的东西,不过不同在于,他其中的数据都是格式化的,因此我们可以利用它去抽取关系信息.

在我们抽取出关系之后就可以得到一个关系三元组,再通过在语料库中搜索两个实体,便可以得到这个关系的 positive example.
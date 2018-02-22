## tensorflow

### 基本结构

-   TensorFlow 变量共享机制

    在进行深度学习是,一个模型中有很多很多的参数,并且我们知道,模型的参数变量不会说随着输入的变化而重新定义,也就是说这些模型参数应该是一个全局变量,但是如果将这些参数设置为一个全局变量就有一个问题,就是...(不太懂,和封装性有关).

    -   因此,TensorFlow提供了**Variable Scope** 这种独特的机制来共享变量.这个机制的使用方法包括两个函数:

        ```python
        tf.get_variable(<name>, <shape>, <initializer>) #创建或返回给定名称的变量
        tf.variable_scope(<scope_name>) #管理传给get_variable()的变量名称的作用域
        ```

        具体的使用方法见下面这个[链接](https://www.jianshu.com/p/ab0d38725f88)

        tf.get_variable,简单来说就是在具体设置某个变量的时候,在之前加一个域名.这个函数和tf.variable的区别是,tf.variable在在同一个name_scope下面,命名了一个重复的变量名后,会自动创建一个新的变量,变量名是一个别名.

        而tf.get_variable会报错.它是不会创建重复变量的,并且不受name_scope的约束.就是说在即使两个作用域下创建两个相同变量的名字也是不可行的.

    -   variable_scope:

        tf.variable_scope()用来指定变量的作用域，作为变量名的前缀，支持嵌套.也就是说,tensorflow中的变量名不止包括变量名,还包括变量名的作用域.这些加起来是作为一个变量的ID存在的.

        但是这个只限于用tf.variable创建的变量,虽然tf.get_variable创建的变量,print出来之后,也是有作用域作为其名字的,但是还是不能用tf.get_variable创建两个同名的变量.

        当使用with tf.variable_scope()打开一个已经存在的作用域时，就会跳转到这个作用域。

        在不同的scope下用tf.variable创建同一个名称的变量是可行的,结果是两者出来的变量的作用域名称是不同的.

### API

-   tf.flags:

    一个用来记录变量名的方法,可以把所有的变量放在一个tf.flags下面,调用的时候用:

    ```python
    tf.flags.DEFINE_integer("num_filters", 128, "annotation")
    FLAGS = tf.flags.FLAGS
    num_filters=FLAGS.num_filters
    ```

-   tf.contrib.learn.preprocessing.VocabularyProcessor (max_document_length, min_frequency=0, vocabulary=None, tokenizer_fn=None)

    处理文本的一个函数,正如函数名所示,是一个处理词汇的前处理函数.

    `max_document_length`: 文档的最大长度。如果文本的长度大于最大长度，那么它会被剪切，反之则用0填充。 

    `min_frequency`: 词频的最小值，出现次数小于最小词频则不会被收录到词表中。 

    `vocabulary`: CategoricalVocabulary 对象。 

    `tokenizer_fn`：分词函数

-   tf.concat(concat_dim, values, name='concat')

    在values的concat_dim这个维度联结起来.

    第一个参数concat_dim：必须是一个数，表明在哪一维上连接.

    如果concat_dim是0，那么在某一个shape的第一个维度上连，对应到实际，就是叠放到列上

-   tf.reshape(tensor, shape, name=None) 

    这个函数很容易理解,重点是当shape中出现-1的时候的情况,这个shape中的-1代表的是:自动计算,例子:

    若tensor的大小是:[3\*3\*4]

    规定的shape是:	[-1*2]

    那么结果就是reshape为[18*2]

-   tf.nn.dropout(h_pool_flat, dropout_keep_prob)

    h_pool_flat为网络的输入的1维数据,dropout_keep_prob为dropout率.

    返回的数据就是一个新的输入层,可以理解为加入掩码的输入,之后就当作输入使用就好了.

-   tf.nn.xw_plus_b(x,w,b,name='')

    return wx+b

-   tf.nn.l2_loss(x)

    return 0.5*pow(x,2)

-   ​
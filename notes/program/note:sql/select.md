### MySQL - Select



**这里我将Mysql语句与select有关的语句框架抽象为:**

$select\ O_a\ A\  from \ O(tables)\ O_b \ B \ C$

$O_b \ B \ C$ 是对行的操作. $O_a\ A$ 是对列的操作.



#### 1. operator on B

**select * from students where ... ;**

> 根据where后面的条件选出合适的条目,这是最常用的 $O_b$

**select gender from students group by gender;**

> group : 和distinct有异曲同工之处, 都是不重复显示前面相同的数据. 和上面的显示结果一致. group决定这次现实的行数. group的本质其实是一个for循环, 
>
> for i in GroupNames:
>
> ​	do something where GroupName == i
>
> 例如,如果 调用  select name from students group by gender;
>
> 即使有100个名字, 也只显示两行 ,分别时一男一女, 分别是男女中最先出现的名字.
>
> select gender,  count(gender) students group by gender;
>
> 会显示出不同性别的人的个数
>
> ```mysql
> +--------+---------------+
> | gender | count(gender) |
> +--------+---------------+
> | 女     |             4 |
> | 男     |             4 |
> +--------+---------------+
> ```

**select * from students order by name desc;**

> order by : 这里的order by和Group, where 都是一样的,都是对表中的整个条目进行处理的, where 是选择条目中的某几项, order是将其排序. group是去重(去重规则由group by 后的规则决定)
>
> desc : 降序　asc:升序



#### 2. comparison operator (B)

> comparison 的基本操作思想是, where 后跟一个bool表达式, 满足bool的数据条目就抽出.更多见[网址](https://dev.mysql.com/doc/refman/8.0/en/comparison-operators.html). 这一部分就是抽象框架中的 $B$ 的部分.
>
> 但是也有不是bool表达式的情况, 例如  GREATEST() 和 LEAST(), 分别返回最大和最小的条目.但是本质的实现肯定也是bool, 也就是最大的(最小的)的条目的bool值为1.

**select * from students where name REGEXP '[a-z]+'**

> REGEXP : 可以认为是like 的进阶版, 可以使用正则.

**select distinct gender from students;**

> distinct : 列找出其中不一样的, 这个命令的结果会显示 男 女 两条.

**select reg_num,name from students where reg_num>1811100;**

> \> : 大于, 用于where的条件中, 选择学记号大于1811100的学生出来.
>
> 除此之外还有 =, <>(不等于) , < , >=, <=, like, in.

**select reg_num,name from students where reg_num>1811100;**

> between a and b : 选出值位于a和b之间的值.
>
> 除此之外还有 not between and 

**select * from students where name like '%田中%'**

> like : 相当于字符匹配, 不过只支持简单的逐一匹配, 可以用 % 代替其他任何字符, 包括空.
>
> STRCMP(expr1,expr2) : 比较两个字符串. 相同返回0, 第一个大于第二个返回1,反之-1.

**select * from students where name is not null**

> is not null : 由于这里的每个条目都有名字,因此会显示所有的条目.

**select * from students where name in (....)**

> 选择几个人名的数据进行输出.

**select name from students where name regexp '[a-z]+' and reg_num > 1811330;**

> 注意两点:
>
> 1. 后面有两个表达式, regexp和 > :说明后面的选择的条件其实与前面要显示的无关， 后面进行判断的范围是条目中的每一项. 而前面的是对于选中条目的再操作.也就是
>
>    do A where B -> if B then A 
>
> 2. 连接符 and ,逻辑符号. 除此之外还有, AND，OR和NOT
>
> 结果如下:
>
> ```mysql
> +---------+
> | name    |
> +---------+
> | zhaoxin |
> +---------+
> ```



#### 3. comparison operator (A)

> where 命令的语法

**select name,reg_num>1811330 from students where name regexp '[a-z]+';**

> 这个式子与上面的相比把最后的 reg_num > 1811330 的限制,放到了前面.结果如下, A和B的区别一览无余. 也就是说, 在这个A里面.这个里面和comparsion operator B 中的效果不同, B中不满足条件的时候, 会拒绝输出那个条目, 而在A中,只会对对应的值赋0或1.
>
> ```mysql
> +----------+-----------------+
> | name     | reg_num>1811330 |
> +----------+-----------------+
> | zhaoxin  |               1 |
> | Phillips |               0 |
> +----------+-----------------+
> ```



#### 4. Operator on C

**select * from students order by name limit 3;**

> limit : 显示最上面的3条条目.
>
> select A from table where(group|order) B limit C
>
> 这个时候我们发现已经有三个不同层次的命令了.之间都是可以组合的.
>
> 其中A只影响向外输出的形式. B和C决定使用那些条目. 其实一定程度上可以认为 B和C 是一个性质的东西.
>
> 



#### 5. Operator on A

> 这里感觉,  $O_a$ 其实是对看待选定数据的方式. 在默认的情况下, 也就是没有使用operator的情况下, 我们是把后面的数据每条当作一个整体来处理的. 因此每个条目都会进行一个输出.
>
> 而 $O_a$ 相当于再次之上又进行了一个约束. 
>
> 比如下面的 max(), 输出的结果就是在经过 $O_b\ B$ 选定的条目的基础上, 以这几个条目作为处理对象,进行了一个计算输出.
>
> 而, count()函数的意义在于, 将 $O_b\ B$ 选定的条目作为输入, 进行去重的操作. 并将不重复的条目的数量进行输出. **

**select max(reg_num) as maxreg from students;**

> max : 这个通常不是用来显示条目信息的的,而是用来显示某一个单值的, 并且可以赋予自己的名字
>
> ```mysql
> +---------+
> | maxreg  |
> +---------+
> | 1811336 |
> +---------+
> ```

**select count(gender) from students; **

> 数数,直接上图:
>
> ```my
> select count(gender) from students;
> +---------------+
> | count(gender) |
> +---------------+
> |             8 |
> +---------------+
> ```
>
> select gender,count(gender) from students group by gender;
>
> ```mysql
> +--------+---------------+
> | gender | count(gender) |
> +--------+---------------+
> | 女     |             4 |
> | 男     |             4 |
> +--------+---------------+
> ```

**select count(gender) from students; **

>```mysql
>+--------------+
>| avg(reg_num) |
>+--------------+
>| 1811227.6250 |
>+--------------+
>```



#### 6. Operation on tables

**select A.name,B.name from students as A, students as B;**

>  列出所有可能的学生组合对,, 包括自己和自己的组合.

**select A.name,B.name from students as A, students as B where A.name!=B.name;**

>  这个里面就排除了自己和自己组合的方式.


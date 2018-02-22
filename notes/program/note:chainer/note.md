### 调试

#### 1. 数据类型错误

##### 1) Actual: U != i 

Expect: in_types[0].dtype.kind == i
Actual: U != i

U 指的是 str 类型, i 指的是 int, 这里有可能是从文件中提取数字时, 没有转换为int型.

#### 2. 无更新问题

##### 1) loss非列表

loss 必须是一个 Variable类型的array, 不能是 Variable 类型的 float
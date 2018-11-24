#### 1. What is the task tackled in this paper?

- entity alignment

#### 2. What is the research question in this paper?

- Previous work has too hard constraints on the tranformation mapping. 
- Previous work can only use the alignment information of two-graphs, cannot make full use of multi-graphs(>2).
- High cost when aligning between N graphs, in which order is O(N!).

#### 3. How to solve it/what is the approach of this paper?

- The framework of model is still be a joint model of two parts : 

  - relational inference model : Regard as a controlled variable. We will use TransE. 
  - Alignment model : replace hard constraints, such as linear transformation and translation constraint, with soft constraints - continuous mapping. 

- How to design continuous mapping loss ?   -- Regards as a optimal transportation problem. 

  - Loss function is the Wasserstein metric between embedding spaces. 

    The matrix $T\in \mathbb{R}^{m*n}$ is the Wasserstein transport mapping matrix between two graph embedding spaces $X=(x_1,...,x_m)\in \Omega^m,Y=(y_1,...,y_n)\in \Omega^n$ .


  - One constraint conditions on this mapping : 

    Mapping must be continuous for distance under its space metric.

    (In other words, Continuous mapping between two spaces, maybe homeomorphism?)

- Training process:

  - Wasserstein metric when align **two graph embedding spaces** with alignment pair $(x_i,y_j)$

    - Simplily take wasserstein distance as loss function. 

    - The transport mapping should satisfy the **constraint** below: 

      $T(x_i, y_j)=\sum_k^n T(x_i, y_k)$ 		$ (x_i\in \mathbb{R}^{m}, y_j\in \mathbb{R}^{n})$

  - Wasserstein metric among **multi-graph embedding spaces** with alignment list $(x_{i_1}^1,...,x_{i_k}^k)$: 

    - Firstly, Calculate the Wasserstein barycenter $S\in \Omega^x$ of n graph embedding spaces .

    - Secondly, The transport mapping should satisfy the **constraint** below: 

      Each entity in alignment list $(x_{i_1}^1,...,x_{i_k}^k)$ has a coresponding distribution $P_{i_j}^j$ which is the $i_j$-th row of transport mapping matrix  $T^j$  between graph embedding space $X^j$ and barycenter $S$. 

      We note it as $(P_{i_1}^1,...,P_{i_k}^k)$.  We need all the distributions in this list to be same. 

    ![](./pictures/)


- Predicting process:

  Predict in the way we trained it on different problems (Two graphs and N-graphs). 

  ​

#### 4. Summary & Questions

##### 1) Questions

- How do you think about this plan, enve it's only theoritcal and not concret now. 
- **Just an opinion:** I think it may be unsatisfactory using Wasserstein distance as alignment loss function. Because it only has very weak constraint on embedding space.

##### 2) Summary

- It's a joint model, because alignment loss is related to relational inference loss.
- I think using the first and second items (in section2) as motivations is better than high computation cost deficiency.
- Anyway, The next step is to find the optimization method and do an experiment to verify the effect. 


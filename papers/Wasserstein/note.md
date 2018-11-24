### Gaussian Word Embedding with a Wasserstein Distance Loss
[Gaussian Word Embedding with a Wasserstein Distance Loss](https://arxiv.org/abs/1808.07016)

#### 1. What is the task tackled in this paper ? 

- Word embedding with distribution. 

#### 2. What is the research question in this paper ? 

- Previous work about word embedding with Guassian distribution take **KL-distance** as loss function. 
- The shortcoming of take KL-distance as distance measure function:
  - KL divergence is ill-defined for two very similar distributions, which is asymmetric. 
  - It is not sensitive to the distance between two distant distributions

#### 3. How to solve it/what is the approach of this paper ?

- Introduce wasserstein as Distance loss when take co-occurence into account, which can fill the gap by KL-distance. 
- Not only wasserstein distance , but also in order to incorporate more information, this reasearch use the external information from ConceptNet. 
- Because relation in ConceptNet like "IsA" is asymmetric, so it model relations in ConceptNet with KL-distance. 




###  Is Wasserstein all you need?

[Wasserstein is all you need](https://arxiv.org/abs/1808.09663v1?utm_campaign=Deep%20Learning%20Weekly&utm_medium=email&utm_source=Revue%20newsletter)

#### 1. What is the task tackled in this paper ? 

- The represetation method of unsupervised learning, which is foucs on **co-occurrence information** of the entities and their context. 

#### 2. What is the research question in this paper ? 

- Point-wise embedding alone is lack of capacity to represent inherent uncertainty and polysemy in entity represetation. 

#### 3. How to solve it/what is the approach of this paper ?

- Use histogram (or probability distribution) over embedding can capture more of this information.  Concretly, word representations take both of distribution anfd point-wise embeddings into account, and as a result, it appear to be a distribution finally. 
- The metric of distance between two word embeddings (distributions) is Wasserstein. 



### *Learning with a Wasserstein Loss*

[Learning with a Wasserstein Loss](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&ved=2ahUKEwjtsKWds-zeAhVHvrwKHfh9C6kQFjAAegQIARAB&url=https%3A%2F%2Farxiv.org%2Fabs%2F1506.05439&usg=AOvVaw0wHvrjcUhdnAhuZjZYqspg)

#### 1. What is the task tackled in this paper ? 

- Multiclass classification

#### 2. What is the research question in this paper ? 

- Output space in multiclass classification task also have some inherent structure, which can not be captured by usual loss metrics like softmax. 
- Note : the structure mentioned here is relation between labels. For example, in imageNet, different label have relation which can be introduced in WordNet. 

#### 3. How to solve it/what is the approach of this paper ?

- Replace the final loss function with Wasserstein based on distribution but not a scalar .
- The prior knowledge of label can be modeled by other method which is dependent on particular tasks. 






### *Supervised Word Mover’s Distance*

[Supervised Word Mover's Distance](https://papers.nips.cc/paper/6139-supervised-word-movers-distance.pdf)

#### 1. What is the task tackled in this paper ? 

- Text classification task based on document distances with a inherent metric. 

#### 2. What is the research question in this paper ? 

- The document metric used in previous works are known as Wasserstein, but it can only deal with the unsupervised task, **lack a mechanism to incorporate supervision** when available. 

#### 3. How to solve it/what is the approach of this paper ?

- Use the two linear transformations to represent the difference when classify different lables. 
  - One is used to transform word embedding, which word meaning in different classificatio tasks is different. 
  - The other one is beside the intuition that different classification tasks and datas sets may value words differently. 






### Wasserstein Propagation for Semi-Supervised Learning

[Wasserstein propagation for semi-supervised learning](https://dl.acm.org/citation.cfm?id=3044841)

#### 1. What is the task tackled in this paper ? 

- Graph-based semi-supervised learning
- For instance, traffic density prediction on a traffic network, which take histograms over the 24-hour cycle on each crossing as the target. 

#### 2. What is the research question in this paper ? 

- The previous work use KL-distance as metric cannot capture intersections between histogram bins.
- The previous work use  tensor product to modify the underlying graph loses prob-
  abilistic structure and tends to be oversmooth. 

#### 3. How to solve it/what is the approach of this paper ?

- Employ the **two-Wasserstein distance** between distributions to construct a regularizer measuring the “smoothness” of an assignment of a probability distribution to each graph node.





### *Fast Computation of Wasserstein Barycenters*

[Fast Computation of Wasserstein Barycenters](https://arxiv.org/abs/1310.4375)

#### 1. What is the task tackled in this paper ? 

- Summarizing and reducing the dimensionality of empirical probability. 
- For example, caculate the mean of a class which each element in it is represented by distribution.

#### 2. What is the research question in this paper ? 

- The choice of divergence defines the mean (barycenter) element. And, Wasserstein barycenters that build upon the subgradient method is has excellent effect on this task. But:
- It has very high cost. 

#### 3. How to solve it/what is the approach of this paper ?

- Some details ... (when needed I will read it)



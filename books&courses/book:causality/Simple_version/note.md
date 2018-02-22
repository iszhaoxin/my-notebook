## Introduction

#### 1. History

Main idea : Probability can not explain causality.

Even the cases which you think have high cause-effect relation, They are still accepting lots of uncertainty influencing their probability. For example:

Take drug and the patients are healed, but it may be other factors of influence here. Like, the patients who can pay for these drug can be taken careful management.

#### 2. Causal Revolution

Begain from 100 years ago, but real development began in 1980s. 

#### 3. Calculus of causation

##### 3.1 causal diagrams

To express what we know

There are lots of kinds expression of causal relation. But the auhtor prefer to use causal diagrams. It likes dot-arrow graph which we will introduce in the latter.

##### 3.2 symbolic language 

###### 1. Language 

To express what we want to know

Explain the difference symbolic rules between statistic and causality inference. 

Here is a example.

- **In statistic**

  $P(L|D)$ : shows that D have correlation with L. For example:

  Falling barometer fall increase the probability of the storm. 

- **In causality**

  There are kinds of formula, here they introduce the $do$ operation.

  $P(L|do(D))$ : shows the probability of L's happen when we **do D**. For example:

  If we force falling barometer to fall, it won't affect the probability of rainning.

###### 2. Inference

And causal revolution gave a way to predict the effect of intervention without actually enacting it. Because we don't have conditions to control everything around the target.

The inference actually include two parts:

- Ask the question, like $do$.
- Devised a way to emulate it by non-invasive means.

#### 4. Counterfactual

What $L$ will be if we did't do $D$. 

The most difficult part in causality.  It will give robot the ability to ask why.

#### 5. Blueprint of reality

##### 5.1 The working mechanism of inference 

It is showed in graph p12.

The detail of the mechanism is in the book, The main point is that:

- The inference consists of statistic and causality, which there is a interaction between them.
- `Assumption` is the important part to model causality, it indicate the main rules to causality inference system.
- `Estimand` is the recipe to cook the data in to answer for query. It will summarize the information of $P(L|do(D))$ but not $P(L|D)$ to the model

##### 5.2 Discussion

- Compared to science which only use data(ML and statistic), causal model can deal with the problem of intervention and counterfactual.

- Counterfactual can make robot to ask "why". And we can use caual model to answer Counterfactual query.

- Causal model has adaptability, because the use of data is after estimand which guarantees the independence between data and estimand.

  On the contrary, deep-learning and statistic don't have the property of adaptability. For example,  The model trained on a set of data only have high accuarcy in that data.

- **Causal lens** to depict a question.

#### 6. Introduction of book's chapters

##### 6.1 Chapter1 

- Ladder of causation : observation $\to$ intervention $\to$ counterfactual
- Basic reasoning with causal diagram

##### 6.2 Chapter2

- Statistic inflicts casual blindless on itself.
- The story of hero.

##### 6.3 Chapter3

- Why traditional AI is wrong and Why author convert to causality?
- A causality-minded Introduction to bayes's rule and Bayesian method of reasoning.
- Some examples of bayes network.

##### 6.4 Chapter4

- Major contribution of statistic to causal inference : Randomized controlled trial, do with $P(L|do(D))$
- A simple solution to the general confounding problem.

##### 6.5 Chapter5

- History

##### 6.6 Chapter6

- Introduce some paradox : show that human intuition is grounded in causal not statistic.

##### 6.7 Chapter7 

The seriously introduction about **Ladder of causation** from chapter7 to chapter9.

- intervention , do-type question
- Causal inference engine : produce yes/no in Figure1.1 

##### 6.8 Chapter8

- Counterfactual 

##### 6.9 Chapter9

- Mediation : to judge if interaction between D and L directly or indirectly

##### 6.10 Chapter10

- strong AI
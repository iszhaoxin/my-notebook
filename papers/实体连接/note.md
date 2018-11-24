
### 2016.03:Neural Architectures for Named Entity Recognition

#### 1. What is the task tackled in this paper ? 

- Named Entity Recognition

#### 2. What is the research question in this paper ? 

- State-of-art works rely heavily on hand-crafted features and domian-soecific knowledge in order to learn efficientively from small, supervisd traning corpora. 

#### 3. How to solve it/what is the approach of this paper ?

- Propose two method:
  - One based on **bidirectional LSTMs** and **CRFs**
  - One constructs and labels segments using **a transition-based approach** inspired by shift-reduce parsers



### 2016.06:Joint Learning of the Embedding of Words and Entities for Named Entity Disambiguation

#### 1. What is the task tackled in this paper ? 

- **Named Entity Disambiguation** : Resolve multiple named entity mentions in a document to their correct references in a knowledge bases. 

#### 2. What is the research question in this paper ? 

- Previous works are too sophisticated. 

#### 3. How to solve it/what is the approach of this paper ?

- Propose a simple method to map word embedding and knowledge embedding into the same continous vector space, which the loss function has three parts:
  - Word embedding loss : Skip-gram
  - KB embedding loss : wikipedia Link-based Measure
  - Anchor context loss : simple conditional probabilstic



### 2017.03:Bridging Text and Knowledge by Learning Multi-Prototype Entity Mention Embedding

#### 1. What is the task tackled in this paper ? 

- **Named Entity Disambiguation** : Resolve multiple naned entity mentions in a document to their correct references in a knowledge bases. 

#### 2. What is the research question in this paper ? 

- Even previous works have embed words and entities into a common space, but the ambiguity still exists beacause same mention alway refers to different entities in different context.

#### 3. How to solve it/what is the approach of this paper ?

- They use the insight in WSD, which one mention can have serveral mention senses and each mention sense can corresponds to a entrie in knowledge graph. 



### 2017.03:Named Entity Recognition in the Medical Domain with Constrained CRF Models

#### 1. What is the task tackled in this paper ? 

- Relation extraction from Medical Domain

- The Relations they want to extract:

  - Independent and dependent relation of two variable

  - Probabilistic statements : 

    The relation of conditional probability statement which recorded in text. 

#### 2. What is the research question in this paper ? 

- Determining the boundaries of the variables(word of entity) is not straightforward. 

#### 3. How to solve it/what is the approach of this paper ?

- Add contraints on CRF



### 2017.04:SWELLSHARK_ A Generative Model for Biomedical Named Entity Recognition without Labeled Data

#### 1. What is the task tackled in this paper ? 

- Named Entity Recognition

#### 2. What is the research question in this paper ? 

- Training data is not enough because:
  - Crowdsourcing is too expersive.
  - Distant supervision is not suitable facing with biomedicine data because any single ontology in it may have widely varying accuracy depending on the target task, making them difficult to combine using simple methods like majority vote. 
- Previous works didn't consider the overlapping problem, which lead to incorrect marginal probability. 

#### 3. How to solve it/what is the approach of this paper ?

- Proposed a method based on weak supervision, which can provide labeled data from unlabled corpus.  
- Use multinomial generative model to solve overlapping problem. 



### 2017.04:Semi-supervised sequence tagging with bidirectional language models

#### 1. What is the task tackled in this paper ? 

- Sequence labeling - NER and Chunking

#### 2. What is the research question in this paper ? 

- Context sensitive representation is important and there is no scheme to include it in sequence labeling model.

#### 3. How to solve it/what is the approach of this paper ?

- Do experiment on NER and Chunking task to show that the context sensitive representation captured in the LM embeddings is useful in the supervised sequence tagging setting. 
- Another iinteresting result demonstrates that domain specific pre-training is not necessary by applying a LM trained in the news domain to scientific papers.



### 2017.05:BCC-NER: bidirectional, contextual clues named entity tagger for gene/protein mention recognition

#### 1. What is the task tackled in this paper ? 

- NER for gene/protein

#### 2. What is the research question in this paper ? 

- No research question

#### 3. How to solve it/what is the approach of this paper ?

- Model consists of three modules:
  - NLP pre-pocessing : text feature extraction and feature selection
  - Machine learning module : training and model building with bidirectional conditional random fields
  - NLP post-pocessing : surrounding text features, parenthesis mismatching
- Boring research...



### 2018.10:Clinical Concept Extraction with Contextual Word Embedding

#### 1. What is the task tackled in this paper ? 

- NER : Automatic extraction of clinical concepts with ELMo

#### 2. What is the research question in this paper ? 

- Although the ELMo model is shown to have a good performance in some NER tasks, but that is on a general domain,  and as result, **does not** **demonstrate a desired performance** for a clinical concept extraction task.

#### 3. How to solve it/what is the approach of this paper ?

- Pretrain ELMo on clinical text and incorprate it into classical NER method (BiLSTM+CRF)




### 2018.10:In-domain Context-aware Token Embeddings Improve Biomedical Named Entity Recognition

- Same as 2018.10:Clinical Concept Extraction with Contextual Word Embedding




### 2018.11:Neural Collective Entity Linking

#### 1. What is the task tackled in this paper ?

- Entity linking with global information

#### 2. What is the research question in this paper ?

- Entity linking has two main method : 
  - Local method only use the context information surrounding target words.
  - Global method can use all information extracted not only in target sentences and words, which sames like the latent variables in casulity inference. 
- Obviously, Global method performs better than local method, but it still suffers from three drawbacks:
  - The global approach suffers from the data sparsity issue of unseen words/entities
  - The joint inference mechanism in the global approach leads to expensive computations, especically when the entity graph may contain hundreds of nodes in case of long documents.
  - The annotated EL training data is usually expensive to obtain or only available in narrow domains, which results in possible overfitting issue or domain bias.

#### 3. How to solve it/what is the approach of this paper ?

- Propose a model with three-components : 
  - Candidate Generation : give serveral entity candidates in graph to one mention in sentence. 
  - Feature Extraction :  extract both local features and global features for each candidate entity to feed neural model
  - Neural Collective Entity Linking : Use neural model to link entities. 


- Propose a novel Neural Collective Entity Linking model (NCEL), which performs global EL combining deep neural networks with **Graph Convolutional Network**
- In GCN model, attention mechanis, is also used to robustly model local contextual information by selecting informative words and filtering out the noise. 


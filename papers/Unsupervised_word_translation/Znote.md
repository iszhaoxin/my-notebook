### 2013.03_Multilingual distributed representations without word

#### 1. What is the task tackled in this paper ? 

- Multilingual language model

#### 2. What is the research question in this paper ? 

- Prvious works require word alignments resources.

#### 3. How to solve it/what is the approach of this paper ?

- Use sentence-level parallel corpus to train the multilingual language model:
  - Use a simple CVM model to model sentence , and make aligned sentences pair in parallel corpus to be close. 



### 2013.10(NIPS):Zero-Shot Learning Through Cross-Modal Transfer

#### 1. What is the task tackled in this paper ? 

- Zero-shot learning for object recognition : predict the class of image which may be unseen in training data with only the description of the image 

#### 2. What is the research question in this paper ? 

- Previous work on zero-shot learning which can only predict intermediate features or differentiate between various zero-shot classes 
- They require manually defined semantic or visual attributes for the zero-shot classes.

#### 3. How to solve it/what is the approach of this paper ?

- Main model : Learn correlation of images and words by mapping them into a common space. The loss function use non-linear transformation, which shown below:

  ![](./pictures/3)

  > w : word embedding 		x : image embedding	
  >
  > f : non-linear function 		s$\theta$ : matrix by real value

- Improve the questions in previous work by two **novelty detection** methods, which both of them are based on the idea of outlier detection. 



### 2013 : Exploiting Similarities among Languages for Machine Translation

#### 1. What is the task tackled in this paper ? 

- Word embedding alignment

#### 2. What is the research question in this paper ? 

- The first reasearch about word embedding alignment

#### 3. How to solve it/what is the approach of this paper ?

- Use simple linear transformation to learn alignment relationship with supervision word pairs. 






### 2014(EACL) : Improving Vector Space Word Representations Using Multilingual Correlation 

#### 1. What is the task tackled in this paper ? 

- Vector Space Word Representations

#### 2. What is the researchquestion in this paper ? 

- Parallel corpora is helpful to advance word embedding. 

#### 3. How to solve it/what is the approach of this paper ?

- Project two embedding spaces into another (may be lower dimension) embedding space. And maximize the correlation of corresponding pairs in that two projection spaces 

- Formula:

  1. Projection:

     ![](./pictures/1)

  2. Maximize Correlation:

     ![](./pictures/2)




### 2014.02_An autoencoder approach to learning bilingual word representations

#### 1. What is the task tackled in this paper ? 

- Bilingual Word Representations learning

#### 2. What is the research question in this paper ? 

- Due to the imbalance of language-specific corpus, the transformation of knowledge in one language to another one tends to be important issue.
- The previous works abour knowledge transformation is concentrated on task with alignment information which is not avaliable in many suition. 

#### 3. How to solve it/what is the approach of this paper ?

- Instead of using word-level alignment information, they use document-level alignment information to train the Bilingual Word Representations.

- Method : Autoencoder

  - Encode source language document embedding and decoder it to target language document embedding.
  - The word embedding is trained as the parameters in the model.




### 2016(ACL)_On_the_Role_of_Seed_Lexicons_in_Learning_Bilingual_Word_Embeddings

#### 1. What is the task tackled in this paper ? 

- Word embedding alignment or Bilingual word embeddings

#### 2. What is the research question in this paper ? 

- The problem about how seed lexicon important is to this task is unclear?

#### 3. How to solve it/what is the approach of this paper ?

- Author do three things in this paper:
  - Classify the existed works about bilingual word representation into four types.
  - Generate several seed lexicon pairs by 
    - translation method(Google translate, translated by other bilingual word embedding model)
    - Reliablity (by mutual distance)
    - Frequency
  - Do experiment on several model and its own model, then do anlaysis on them, which induce three results:
    - External resource of word-level alignment is unnecessary, and some inexpensive autimatically induced lexicons even do better. 
    - Choice of seed lexicon pairs may strongly influence the properties of the result
    - The highly frequent words recieve more accurate representations than low frequency words. 




### 2014.06(ACL):Is this a wampimuk? Cross-modal mapping between distributional semantics and the visual world

#### 1. What is the task tackled in this paper ?

- Zero-shot learning for object recognition : predict the class of image which may be unseen in training data with only the description of the image 

#### 2. What is the research question in this paper ?

- The word embedding embeded only on co-occurrence without external signal has two drawbacks from cognitive point:
  - Grounding problem :  Models have no access to visual and perceptual information , which will cause the lack of ability to capture the grounded knowledge that human have about the world. 
  - Lack of reference : The ambiguity between meanings of two words caused by their co-occurrence can be sovled by external data like images. (For example, cat and dog)

#### 3. How to solve it/what is the approach of this paper ?

- Embed images and words individually into $V_s,W_s$

- Train four different classification models to do cross-modal mapping.

  - Linear regression : 

    ![](./pictures/4)

  - Canonical Correlation Analysis: (map vectors into a low-rank space)

    ![](./pictures/5)

  - SVD 

  - NeualNet(Best):

    ![](./pictures/6)

    ​

  ​

### 2014.10(ICLR) Improving zero-shot learning by mitigating the hubness problem

#### 1. What is the task tackled in this paper ? 

- Zero-shot learning : Learn a function to transform one feature space into a word vector space. This function can be used to transform some unknow object in the feature space into word vector space and the use the neighbour nearest method to find the corresponding word vector. 

#### 2. What is the research question in this paper ? 

- The neighbourhoods of the mapped elements are strongly polluted by hubs, which cause problems when finding the corresponding pairs based on neighbourhood nearest. 

#### 3. How to solve it/what is the approach of this paper ?

-  Take the **proximity distribution** of potential neighbours across many mapped vectors into account.
- In other word, Use the topology of the subspace where the mapped point（point in target space ）lives to correct nearest neighbour retrieval. Two concrete method:
  - A simple way to correct for this is to **normalize the vector of similarities of each target** item to the mapped pivots to length 1
  - After k-NN retrive use the cosine to caculate the similarities. 



### 2015.03(NAACL):Deep Multilingual Correlation for Improved Word Embeddings

#### 1. What is the task tackled in this paper ? 

- Word embeddings

#### 2. What is the research question in this paper ? 

- Lack of using multilingual context to enhance word embeddings
- Preposed-work only use linear projection which has strong limition on the spaces transformation. (is not sufficiently powerful to capture the hidden, non-linear relationship)

#### 3. How to solve it/what is the approach of this paper ?

- Adding multilingual context when learning embeddings can improve their quality
- **Propose a non-linear transformation** method, which using the recently proposed deep canonical correlation analysis. Concretly, they proposed two methods.
  - Deep CCA : use deep neural network to extract features from source and target embedding set and trained to maximize the correlations between outputs in the two views. The correlation is measuresd same as linear CCA (2014. EACL)
  - Use kernel CCA, which introduce ninlinearity from kernel functions. 



### 2015.08(ACL):Hubness and Pollution: Delving into Cross-Space Mapping for Zero-Shot Learning

#### 1. What is the task tackled in this paper ? 

- Zero-shot learning

#### 2. What is the research question in this paper ? 

- Hubness : obvious in cross-lingual problem.
- Pollution :  obvious in cross-modal mapping suffers from overfitting issues, which means that most returned label in test are annotated with a label coming from the training data

#### 3. How to solve it/what is the approach of this paper ?

- Use margin-based loss function to reduce hubness problem caused by least-squared loss occurred cross-space mapping 
- Augment the training data with automatically constructed examples to solve pollution problem. 



### 2015 : Normalized Word Embedding and Orthogonal Transform for Bilingual Word Translation

#### 1. What is the task tackled in this paper ? 

- Supervised word embedding alignment 

#### 2. What is the research question in this paper ? 

- Both the word embedding and the linear transform are ill-posed, due to the inconsistence among 
  - the word embedding objective function, **(inner product)**
  - the distance measure for word vectors **(cosine)**
  - and the object function to learn linear transformation. **(mean squared error)**

#### 3. How to solve it/what is the approach of this paper ?

- This paper solve the inconsistence by normalizing the word vectors : make inner product and cosine to be the same. $||WX-Y|| \to (WX)^TY$
- The linear transformation has to be constrained as an orthogonal transform : to keep normalize constraint. $min_{\hat{W}}||W-\hat{W}|| \ \ s.t. \hat{W}^T\hat{W}=I$




### 2016.01:Ten Pairs to Tag

#### 1. What is the task tackled in this paper ? 

- Supervised(little data) word embedding alignment 

#### 2. What is the research question in this paper ? 

- It is less clear what can be done without extensive parallel resources, that is to say, use as little data as possible 

#### 3. How to solve it/what is the approach of this paper ?

- They enable transfer of information from the source to target languages by establishing a coarse mapping between two languages based on few available translation pairs
- Use POS tagging model (HMM) to fine tune the correspondence relation. 




### 2016.08(Miceli):Towards cross-lingual distributed representations without parallel text trained with adversarial autoencoders

#### 1. What is the task tackled in this paper ? 

- Unsupervised word embedding alignment 

#### 2. What is the research question in this paper ? 

- They use parallel resource, which need more cost.

#### 3. How to solve it/what is the approach of this paper ?

- Based on a assumption that a shared semantic structure between languages
  is strong enough that it allows to induce compatible distributed representations without using any parallel resource. 
- They propose a method using GAN to learn corresponding relation between two languages , which regard word embedding space as a random process. 
  - Generator turn source embedding into transformed embedding
  - Discriminator estimate the probability of transformed embedding to be sampled from target word embedding space. 




### 2016.10(COLING) :A Distribution-based Model to Learn Bilingual Word Embeddings

#### 1. What is the task tackled in this paper ?

- Unsupervised word embedding alignment

#### 2. What is the research question in this paper ?

- Previous works are all rely on supervison information. And the reliance on supervision might limit the development and application of cross-lingual resperesntion.


#### 3. How to solve it/what is the approach of this paper ?

- Based on the assumption that monolingual embeddings have similar geometric shape, they desire to unify two embedding distributions with same featrue. 
  - Concretly, they match feature space distributions of source and target domain by keep accordance of their means and variances. 




### 2016.11(Artetxe):Learning principled bilingual mappings of word embeddings while preserving monolingual invariance

#### 1. What is the task tackled in this paper ? 

- Supervised word embedding alignment

#### 2. What is the research question in this paper ? 

- There is no summary work about Supervised word embedding alignment task

#### 3. How to solve it/what is the approach of this paper ?

- Unify previous works into a framework, and propose a novel method based on that. 

- The new method:

  - Unify the two low-rank projections about embedding space transformation in CCA, which change:

    ![](./pictures/7) (with different transformation matrix v!=w)

    to

    ![](./pictures/8)(with same transformation matrix v=w)

  ​

### 2017.01:Learning bilingual word embeddings with no(almost) bilingual data

#### 1. What is the task tackled in this paper ? 

- Weakly word embedding alignment

#### 2. What is the research question in this paper ? 

- Dictionaries of the big size are not readily available for many language pairs, but they are needed in previous works.

#### 3. How to solve it/what is the approach of this paper ?

- I must to say that : It did a very good survey job. 
- Reduce the need of large bilingual dictionaries to much smaller seed dictionaries. 
- Insight of method is using predicted pair as seed dictionary by mapping method to strength mapping method in next iteration. 
  - Use a closed-form solution of transformation matrix to speed up optimization.
  - Use inner product to avoid hubness problem with k-NN prediction. 




### 2017.02(Smith)Offline bilingual word vectors, orthogonal transformations and the inverted softmax

#### 1. What is the task tackled in this paper ? 

- Supervised word embedding 

#### 2. What is the research question in this paper ? 

- hubness

#### 3. How to solve it/what is the approach of this paper ?

- Use inverted softmax to estimate the similarity between two embeddings so that hubness will also have a large denominator which will evilate the hubness problem. 

  ![](./pictures/9)

  ![](./pictures/10)

- And they propose other two methods to help supervision:

  - Identical character strings : different words in different languages which have same string have same meaning.
  - Aligned sentence : use mean embedding of words in aligned sentence to provide weak supervision for this task. 




### 2017.03(IJCAI)_Bootstrapping Unsupervised Bilingual Lexicon Induction

#### 1. What is the task tackled in this paper ?

- Unsupervised word embedding alignment 

#### 2. What is the research question in this paper ?

- Most of the prior work depend on parallel data in the form of a small bitext and Other prior work assumes access to additional resources or features

#### 3. How to solve it/what is the approach of this paper ?

- First create a seed set of translation pairs, and then iteratively expand the lexicon with a bootstrapping procedure. And the seed set is constructed by identifying words with similar spelling. (Same with 2017.02(Smith))

- then use this noisy seed lexicon to train context vectors via neural network (Linear mapping)

  ​

### 2017.07(EMNLP) : Earth Mover’s Distance Minimization for Unsupervised Bilingual Lexicon Induction

#### 1. What is the task tackled in this paper ?

- Word embedding alignment

#### 2. What is the research question in this paper ?

- No unsupervised method : previous works are all doing with the cross-lingual supervision.(Very strange here, because there is lots of previous work dealing with unsupervised alignment problem)

#### 3. How to solve it/what is the approach of this paper ?

- The structural isomorphism across monolingual embedding spaces points to the feasibility of this task. And the way to use this information is Earth Mover's Distance.

- Concretely , they propose two methods :

  - WGAN : Generator transform source embedding space into target space , which discriminator cacluate the wasserstein distacne between this two spaces as loss. 

    > All the technique here is all by WGAN, nothing new. 

  - EMDOT : Minimum wasserstein distance between transoformed source embedding space and target embedding space, which the transformation is constrained by orthogonal group  $O(d)$

    > Optimization is performed by iteration on **space transformation matrix G** and **word embedding corresponding transportion matrix T**.



### 2017.09(Zhang,ACL):Adversarial Training for Unsupervised Bilingual Lexicon Induction

#### 1. What is the task tackled in this paper ? 

- Unsupervised word embedding 

#### 2. What is the research question in this paper ? 

- Previous endeavors to connect separate monolingual word embeddings typically **require cross-lingual signals as supervision**

#### 3. How to solve it/what is the approach of this paper ?

- Provide a unsupervised method to align word embedding in two spaces: Same like the research 2016.08(Miceli), they use GAN to train the mapping relation.

- Intuition: find the mapping function to make source embedding space seem to follow the distribution of generating target embedding space. They propose three different models shown below: Generator do mapping , Discrimator do distinguishment. 

  ![](./pictures/11)

- The most interesting part is that they use a **orthogonal parametrization** Neural network to be the generator. 






### 2017.10:Word translation without parallel data

#### 1. What is the task tackled in this paper ? 

- Learning cross-lingual word embeddings

#### 2. What is the research question in this paper ? 

- State-of-the-art methods for learning cross-lingual word embeddings have relied on bilingual dictionaries or parallel corpora. 
- There are several method based on unsupervised data, but their performance is significantly below supervised methods. 

#### 3. How to solve it/what is the approach of this paper ?

- They build a bilingual dictionary between two languages without using any parallel corpus, by aligning monolingual word embedding spaces in **an unsupervised way**. 

- Same like 2017.09(Zhang,ACL), their method is still based on adverisal training, but they made three change:

  1. Change the generator by a linear mapping.
  2. After adverisal learning, they use high-quality seed pairs (high frequency and mutual nearest pair) as seed to train usual orthogonal linear mapping matrix. 
  3. Use a mutual mapping nearest neighbour loss to solove the hubness problem which is different with previous work. 

  The final loss function looks like:

  ![](./pictures/12)

  ![](./pictures/13)



### 2017.11(TACL)_Semantic Specialisation of Distributional Word Vector Spaces using

#### 1. What is the task tackled in this paper ? 

- Bilingual word representation

#### 2. What is the research question in this paper ? 

- There is no previous work use BabelNet as cross-lingual lexical resource to help training Bilingual word representation. 

#### 3. How to solve it/what is the approach of this paper ?

- Proposed method is :

  - Load pre-trained word embedding 

  - Inject synonymy information and antonymy information of different languages into pre-trained embeddings. The loss function is :

    ![](./pictures/14)



### 2018.05(ACL)_A robust self-learning method for fully unsupervised cross-lingual mappings of word embedding

#### 1. What is the task tackled in this paper ? 

- Unsupervised Cross-lingual word embeddings alignment

#### 2. What is the research question in this paper ? 

- Previous unsupervised methods about learrning cross-lingual word embeddings alignment can be classified into three type:
  - Use some tricks to induct seed lexicon from scratch, which dependent on the target and source language. 
  - Use adverisal learning to learning mapping
  - Use earth mover's distance 
- Both of the last two need some reliable resource. 

#### 3. How to solve it/what is the approach of this paper ?

- Propose a method using the insight that:
  - Two equivalent words in different languages should have a **similar distribution**, these property can be used to induct initial set of word pairing. 
  - **The distribution here** is most difficult to measure. Here author uses the similarity with other words in their language as distribution. 
- The proposed method is consist of four part :
  - Normalize the embedding to make them units.
  - **Conduct the distribution :** Sort the similarity matrix by row's value, which the values in each row are sorted independently from other rows. This operation corresponde to the permutation of columns and rows, and is admitted between two isometric space(等距的空间). 
  - Use the seed inducted in last step to learn linear mapping. And four tricks are used here. Iteratively repeat the second and third steps.
  - Use symmetric re-weighting method to get the final good result. 





### 2018.05(NIPS) : Unsupervised Alignment of Embeddings with Wasserstein Procrustes

#### 1. What is the task tackled in this paper ?

- Unsupervised Alignment of word Embeddings

#### 2. What is the research question in this paper ?

- Previous works have difficulties to lead a fast and stable optimzation result and is very sensitive to the initialization. 

#### 3. How to solve it/what is the approach of this paper ?

- Propose a simple approach based on **jointly learning** the alignment and the linear transformation between the two point clouds. 
- Concretly, this paper proposed three techniques on optimizaiton:
  - Use subsamples instead of all samples at each batch
  - Use gradient-guided step in optimization of Q(space transformation)
  - Use a relaxation formula make orginal problem be convex. 
- Import new evaluation metric(Inverted softmax) to avoid hubness problem with the k-NN method. 




### 2018.06:Non-Adversarial Unsupervised Word Translation

#### 1. What is the task tackled in this paper ? 

- Unsupervised word translation

#### 2. What is the research question in this paper ? 

- Adverisal learning based method suffer from the typical drawbacks of generative adversarial models: sensitivity to hyper-parameters, long training time and lack of interpretability. 

#### 3. How to solve it/what is the approach of this paper ?

- Take unsupervised word alignment as a 3D point cloud matching problem. 

- Because word distribution is quiet different from 3D point clouds : 

  - They are much higher dimensional
  - It is not obvious a priori that different languages present different “views” of the same “object” and share exactly the same axes of variation. 

  So they set a assumption that the **many language pairs share some principle axes of variation**. 

- Finally the method looks like this:

  - Use PCA to estimate the principle axes. 
  - Use Iterative Closeat Poing method to training the transformation matrix. 




### 2018.08(EMNLP):Gromov-Wasserstein Alignment of Word Embedding Spaces

#### 1. What is the task tackled in this paper ? 

- Unsupervised Alignment of word Embeddings

#### 2. What is the research question in this paper ? 

- Previous works like Conneau et al.,2018; Artetxe et al., 2018., which using orthogonal mapping method, they require either careful initial guesses or postmapping refinements

#### 3. How to solve it/what is the approach of this paper ?

- The reason why they use orthogonal mapping is that the distance between the cross-point is not avaliable. So they use the assumption that two spaces have a orthogonal mapping relation to set a distance metric. 

- This paper use Gromov Wasserstein distance to solve the problem of unacquirable distance, the insight is that:

  Gromov Wasserstein distance don't focus on two points in different spaces, but measure the two distance in two spaces, which need four points. The fomula is shown below:

  $D=L(C_{ik},C_{jl}'),C_{ik}=d(x^{(i)},x^{(k)}),,C_{jl}=d(y^{(j)},y^{(l)})$




### 2018.09_Concatenated Power Mean Word Embeddings as Universal Cross-Lingual Sentence Representation

#### 1. What is the task tackled in this paper ? 

- Sentence embedding 

#### 2. What is the research question in this paper ? 

- Previouse work use BiLSTM to embed Sentence embedding outperformances methods use simple average mean. But they are only usful in specific task which is lack of university. 

#### 3. How to solve it/what is the approach of this paper ?

- Use concatenated embedding + power mean to embed sentence embedding. 

  - Concatenated embedding : concatenated various source embedding avaliable on internet. 

  - power mean : 
    $$
    \big(\frac{x_1^p+...+x_n^p}{n}\big)^{1/p}
    $$

- This model can be state-of-the-art on wide range of downstream NLP tasks. 

- When use embedding trained on cross-lingual corpus, this model can be generalize to corss-lingual tasks like NMT and cross-lingual search.



### 2018(AAAI)_Generalizing and improving bilingual word embedding mappings with a multi-step framework of linear transformations

#### 1. What is the task tackled in this paper ? 

- Supervised word embedding alignment

#### 2. What is the research question in this paper ? 

- A lot of methods was proposed and we need a general framework to to fit all of them.

#### 3. How to solve it/what is the approach of this paper ?

- Summarize all the previous works and incorprate them into one multi-step framework. 
- Run experiment on combination of possiable multi-step and give a evaluation on that. 



### 2018(ICLR)_Unsupervised Machine Translation Using Monolingual Corpora Only

#### 1. What is the task tackled in this paper ? 

- Unsupervised machine translation

#### 2. What is the research question in this paper ? 

- There have been numerous attempts to extend these successes to low-resource lan-
  guage pairs, yet requiring tens of thousands of parallel sentences. But not avaliable for a new language

#### 3. How to solve it/what is the approach of this paper ?

- They propose a model that takes sentences from monolingual corpora in two different languages and maps them into the **same latent space**, based on adverisal learning method.

  - Initinilize model with data pairs generated by a naive unsupervised word-to-word level machine translation system. 

  - Generator : Deniosing auto-encoder

    - Noise : the noise is inject into input sentence to avoid overfitting and usless learning. 

    - Auto-encoder : translate source langauge into a latent space first and translate it again into target space.

      ![](./pictures/15) 

  - Discriminator : 

    To produces a binary prediction about the **language** of the **encoder input sentence**. 

    The insight is to fool discriminate about which latent coding comes from. 



### 2019(ICLR)_Unsupervised Hyper-alignment for Multilingual Word Embeddings

#### 1. What is the task tackled in this paper ? 

- Aligning continuous word representations

#### 2. What is the research question in this paper ? 

- There is no previous work to align multiple languages to a common space efficiently. 

#### 3. How to solve it/what is the approach of this paper ?

- They align multiple languages **simultaneously** in **a common space** in a way that enforces transitive translations. 

- This method is based on papers below and the improvement is only try to deal with multiple languages simultaneously with a efficient way. 

  > (2018.05(NIPS) : Unsupervised Alignment of Embeddings with Wasserstein Procrustes)

- The efficient method is achieved mapping all of them into a common project space. And there are two tricks on loss function:

  - Choice of weights on different language pairs
  - To avoid hubness, use the CSLS loss function. 






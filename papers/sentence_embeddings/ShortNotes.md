2018.09_Concatenated Power Mean Word Embeddings as Universal Cross-Lingual Sentence Representation

1. What is the task tackled in this paper ?

- Sentence embedding 

2. What is the research question in this paper ?

- Previouse work use BiLSTM to embed Sentence embedding outperformances methods use simple average mean. But they are only usful in specific task which is lack of university. 

3. How to solve it/what is the approach of this paper ?

- Use concatenated embedding + power mean to embed sentence embedding. 
  - Concatenated embedding : concatenated various source embedding avaliable on internet. 
  - power mean : 
    \big(\frac{x_1^p+...+x_n^p}{n}\big)^{1/p}
- This model can be state-of-the-art on wide range of downstream NLP tasks. 
- When use embedding trained on cross-lingual corpus, this model can be generalize to corss-lingual tasks like NMT and cross-lingual search.




---
layout: post
title: What are trends in breast cancer research over the past 20 years?
published: true
---

## Summary
* I applied unsupervised machine learning to examine 12,951 review paper abstracts in breast cancer research. 
* I started with the question: "How can I quickly and comprehensively understand the landscape of cancer research?"
* Chemotherapy is most overwhelmingly mentioned in breast cancer research. 
* A topic on the rise is Triple Negative Breast Cancer (TNBC). 
<p/>
<br/>

## Introduction
Breast cancer is one of the major causes of death in the world. Because there are hundreds of thousands of research papers in the field, it is not easy to understand the state of research by looking through papers manually. 

[Topic Modeling](https://en.wikipedia.org/wiki/Topic_model) is an [unsupervised machine learning](https://en.wikipedia.org/wiki/Unsupervised_learning) algorithm that extracts topics from a set of documents. The specific topic modeling algorithm I applied was [Latent Dirichlet Allocation](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation) (LDA), which looks for words that frequently appear together. 

While [cancer.gov](https://www.cancer.gov/research/areas) provides a broad overview of cancer research areas, topic modeling allows us to detect proportionalities and trends, as well as specific medical terms used in the field. 

## Data
Using the [Biopython API](https://biopython.org/DIST/docs/api/Bio.Entrez-module.html). I retrieved 12,951 review paper abstracts published between 1997 to 2017 from the [PubMed database](https://www.ncbi.nlm.nih.gov/pubmed). I narrowed down papers using the following search criteria:
* contains keywords "breast cancer"
* published between 1997 and 2017
* of type: [Review](https://en.wikipedia.org/wiki/Review_article)
* contains an abstract

Reviews are secondary sources that summarize the "current state of understanding" in a field. Because they are typically more qualitative than primary research, I expected that they could provide good, if not better results. I further reduced the amount of text that I had to process by using abstracts. 

## Method
The entire process involved the following steps. The code can be found in my github [repository](https://github.com/edmondchensj/cancer-research). 
1. Data Retrieval

2. Preprocessing
    * I applied Natural Language Processing (NLP) to prepare the data. On top of the standard NLP procedures, I removed additional [stop words](https://en.wikipedia.org/wiki/Stop_words) that are specific to the biomedical field. Examples include "breast", "research", "female", "treatment" and "receptor". 
    * These words describe broad categories and are not specific enough to be helpful. Choosing the stop words took about 10 manual iterations. 
    * The total number of additional stop words was 148. 


3. Topic Modeling
    * I ran an LDA model with Term Frequency-Inverse Document Frequency (TFIDF), using the open source library [*Gensim*](https://radimrehurek.com/gensim/index.html). 

4. Postprocessing and Visualization
    * Visualizations were made using [*matplotlib*](https://matplotlib.org).


## Results
### I. Determining number of topics
The first step was determining the number of topics. Because LDA requires pre-determining the number of topics, I ran LDA for 5 trials, over a span of 30 numbers of topics, and measured the coherence score. 
![coherence-graph](/assets/images/breast_cancer/coherence_graph.png)
The results shows an "elbow" at 11. This number of topics would likely give us best result while being most concise. 

<br/>
### II. So, what are topics?
After deciding on the number of topics, I visualized the topics in a grid of wordclouds below. The wordclouds show the top 20 words for that topic, sized by its importance. 
![wordcloud](/assets/images/breast_cancer/wordcloud_grid.png){: .img-container}

This could be difficult to read in first glance. I looked up the key terms in each topic for better explanations. It seems that these topics **often have subtopics** within themselves. 

... insert table

<br/>
### III. Which is most prevalent? 
![Topic-Mentions](/assets/images/breast_cancer/topic_mention.png){: .img-container}
Mainstream treatment methods, such as **chemotherapy** are mentioned far more frequently than any other topic. Also in this category are hormonal therapy drugs such as **aromatase-inhibitors** and **Tamoxifen**. In chemotherapy, a [**combination**](https://www.ncbi.nlm.nih.gov/books/NBK13955/) approach is common. These treatment methods are often tested [with one another](https://www.ncbi.nlm.nih.gov/pubmed/15894097). One metric often used is the **recurrence** of cancer. 

The next most frequent topics were **metastasis** and **vaccines**.  

<br/>
### IV. What is the trend? 
![Topic-Trend](/assets/images/breast_cancer/topic_trend_rel.png){: .img-container}
It seems that "TNBC" or **Triple Negative Breast Cancer** is a topic that is being increasingly mentioned. Other trending topics include **metastasis**. 

<br/>
## Conclusion
The main subject of chemotherapy is mentioned far more frequently than any other topics. We can observe that in the field of chemotherapy, they often apply **combinations** of agents 

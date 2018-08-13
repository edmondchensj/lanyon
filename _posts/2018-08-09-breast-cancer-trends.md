---
layout: post
title: What are trends in breast cancer research over the past 20 years?
published: true
---

## Summary
* I applied unsupervised machine learning to examine 12,951 review paper abstracts in breast cancer research.
* I wanted to address the question: "How can I quickly understand the landscape of cancer research?"
* While online databases such as PubMed provide access to research papers, it can be challenging to understand what the major frontiers are. 
* Results from unsupervised learning show that chemotherapy is most overwhelmingly discussed, and an emerging topic is Triple Negative Breast Cancer (TNBC). 

<p/>
<br/>

## Motivation
I started this project with the question: 
<div class="message">"How can I quickly understand the landscape of cancer research?"
</div>

Online databases such as PubMed and Google Scholar now provide us with unprecedented access to millions of research papers, if not in full, then at least their abstracts. 

But to search and use these papers, one must first know relevant keywords to search for. This presents a **"cold-start" problem** for 
(a) people who do not have significant prior knowledge in the field and
(b) people who may be deeply involved in a particular subfield and wish to understand what research may lie beyond their horizon. 

A simple approach would be to search "cancer" and start reading the *most cited* papers. This not only is labor-intensive, but the *most cited* papers also do not necessarily represent a diverse range of research areas. 

Another approach would be to look at categories described by cancer institutes such as [cancer.gov](https://www.cancer.gov/research/areas). While these institutes provide qualitative descriptions, it does not provide the most prominent medical terms, nor enable us to detect **distributions** and **trends** of topics in the field. 

## A Solution
[Topic Modeling](https://en.wikipedia.org/wiki/Topic_model) is an [unsupervised machine learning](https://en.wikipedia.org/wiki/Unsupervised_learning) algorithm that extracts topics from a set of documents. The specific topic modeling algorithm I applied was [Latent Dirichlet Allocation](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation) (LDA), which looks for words that frequently appear together. I coupled it with [Term Frequency-Inverse Document Frequency](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) (TFIDF), which prioritizes rare words over common words.

While this would not provide a deep understanding of any particular subfield of research, it does provide the following:
* A comprehensive set of prominent medical terms used across different subfields 
* An ability to measure which subfields might be more popular
* An ability to measure which subfields might be trending

Most directly, it presents a solution to the "cold-start" problem as mentioned above. 

## Data
Using the [Biopython API](https://biopython.org/DIST/docs/api/Bio.Entrez-module.html). I retrieved 12,951 review paper abstracts published between 1997 to 2017 from the [PubMed database](https://www.ncbi.nlm.nih.gov/pubmed). I narrowed down papers using the following search criteria:
* contains keywords "breast cancer"
* published between 1997 and 2017
* of type: [Review](https://en.wikipedia.org/wiki/Review_article)
* contains an abstract

Reviews are secondary sources that summarize the "current state of understanding" in a field and thus present a concise but comprehensive source of data. I further reduced the amount of text that I had to process by using abstracts. 

## Method
The entire process involved the following steps. 
1. Data Retrieval

2. Preprocessing
    * I applied Natural Language Processing (NLP) to preprocess the data. 
    * Using the NLP toolkit [*spaCy*](https://spacy.io/), I kept only **proper nouns** and **nouns**, removing all other forms of words such as verbs and adjectives. This not only removed superfluous words, it might also beneficially remove verbs or adjectives that reflect a biased (positive or negative) sentiment of a particular topic. 

    * I also removed additional [stop words](https://en.wikipedia.org/wiki/Stop_words) that are specific to the biomedical field. Examples include "breast", "female", "treatment" and "receptor". Often, these words describe broad categories and are not specific enough to be helpful. 
    * This took about 10 iterations of topic modeling and manually checking to see which terms I could prune.
    * The total number of additional stop words was 148. 

3. Topic Modeling
    * I ran an LDA model with TFIDF, using the open source library [*Gensim*](https://radimrehurek.com/gensim/index.html). 

4. Postprocessing and Visualization
    * Visualizations were made using [*matplotlib*](https://matplotlib.org).

## Results
### I. Determining number of topics
Once I was satisfied with data preprocessing, the first step was to determine the number of topics. Because LDA requires pre-determining the number of topics, I ran LDA for 5 trials, over a span of 30 numbers of topics, and measured the *coherence score*. 
![coherence-graph](/assets/images/breast_cancer/coherence_graph.png)
The results shows an ["elbow"](https://en.wikipedia.org/wiki/Elbow_method_(clustering)) at 11, after which the rate of improvement reduces significantly. This number of topics would likely give us best result while being most concise. 

<br/>
### II. So, what are topics?
After deciding on the number of topics, I visualized the topics in a grid of wordclouds below. The wordclouds show the top 20 words for that topic, sized by its importance. 
![wordcloud](/assets/images/breast_cancer/wordcloud_grid.png)

This could be difficult to read in first glance. I looked up a few key terms in each topic and provided links to some articles I found. 

<table>
  <thead>
    <tr>
      <th>Topic</th>
      <th>Keywords</th>
      <th>Likely to be about ...</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>metastasis, vaccines, microenvironment</td>
      <td><a href="https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5645504/">Immunotherapy</a> for treating metastatic breast cancer</td>
    </tr>
    <tr>
      <td>2</td>
      <td>vitamin, diet, alcohol</td>
      <td>Macro- and micronutrients such as <a href="https://jamanetwork.com/journals/jama/article-abstract/187252">dietary intake</a> and <a href="https://academic.oup.com/ajcn/article/85/6/1586/4633053">Vitamin D</a></td>
    </tr>
    <tr>
      <td>3</td>
      <td>cscs, taxane, bone metastasis</td>
      <td>Breast cancer stem cells (CSCs) and <a href="https://www.nejm.org/doi/full/10.1056/nejmoa064320">chemotherapeutic agents</a> for metastatic cancer</td>
    </tr>
    <tr>
      <td>4</td>
      <td>cox, autophagy, mutation, mmp</td>
      <td>Enzyme <a href="https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2361146/">cyco-oxgenase (COX)</a>, <a href="http://journals.plos.org/plosone/article?id=10.1371/journal.pone.0006251">autophagy</a> linked to chemotherapy resistance, <a href="https://bmccancer.biomedcentral.com/articles/10.1186/1471-2407-9-188">matrix metalloproteinases (MMPs)</a></td>
    </tr>
    <tr>
      <td>5</td>
      <td>sentinel node, axillary dissection, brain metastasis</td>
      <td>Breast cancer <a href="https://www.cancer.gov/about-cancer/diagnosis-staging/staging/sentinel-node-biopsy-fact-sheet">frequently spreads</a> to axillary lymph nodes.</td>
    </tr>
    <tr>
      <td>6</td>
      <td>tnbc, thyroid, labc, nac</td>
      <td><a href="https://www.jci.org/articles/view/45014?elq=2f1e11aad7e740cf9a3d8bfd51c3b4f4">Triple Negative Breast Cancer</a> (TNBC). Link between <a href="https://breast-cancer-research.biomedcentral.com/articles/10.1186/bcr609">thyroid diseases and breast cancer</a>. Locally advanced breast cancer (LABC). NAC likely refers to neoadjuvant chemotherapy, but could also refer to nipple-areola complex.</td>
    </tr>
    <tr>
      <td>7</td>
      <td>bcrp, biologic therapies</td>
      <td><a href="https://www.nature.com/articles/1206938">Breast cancer resistance protein</a> (BCRP).</td>
    </tr>
    <tr>
      <td>8</td>
      <td>man, population, BRCA, ixabepilone</td>
      <td><a href="https://www.sciencedirect.com/science/article/pii/S0140673606682263">Male breast cancers</a>, <a href="https://www.cancer.gov/about-cancer/causes-prevention/genetics/brca-fact-sheet">BRCA</a> gene mutation, <a href="https://www.cancer.gov/about-cancer/treatment/drugs/ixabepilone">Ixabepilone</a> (chemotherapy for advanced breast cancer)</td>
    </tr>
    <tr>
      <td>9</td>
      <td>obesity, lymphedema, yoga</td>
      <td>Physical activities: <a href="http://cancerres.aacrjournals.org/content/67/6/2391.short">obesity</a>, <a href="https://www.nejm.org/doi/full/10.1056/nejmoa0810118">weight lifting and lymphedema</a></td>
    </tr>
    <tr>
      <td>10</td>
      <td>mirna, zoledronic acid</td>
      <td>MicroRNA <a href="http://cancerres.aacrjournals.org/content/65/16/7065.short">gene expression</a>, zoledronic acid treats <a href="https://en.wikipedia.org/wiki/Zoledronic_acid">bone-related symptoms</a> of cancer</td>
    </tr>
    <tr>
      <td>11</td>
      <td>chemotherapy, combination, tamoxifen</td>
      <td>Conventional treatment such as chemotherapy and hormonal therapy (Tamoxifen). Chemotherapy regimens often use multiple drugs in <a href="https://en.wikipedia.org/wiki/History_of_cancer_chemotherapy#Combination_chemotherapy">combination</a>.</td>
    </tr>
  </tbody>
</table>
<br/>
Interestingly, a common theme among the terms and articles seems to be **emerging resistance** to chemotherapy drugs. Chemotherapy resistance is implicated in terms such as [autophagy](http://journals.plos.org/plosone/article?id=10.1371/journal.pone.0006251), [BCRP expression](https://www.nature.com/articles/1206938), and combination chemotherapy. 

A similar problem is occurring with [antibiotic resistance](https://www.cdc.gov/drugresistance/index.html). In fact, combination chemotherapy was [inspired](https://en.wikipedia.org/wiki/History_of_cancer_chemotherapy#Combination_chemotherapy) by the success of antibiotics in the mid 1900s, which had used a combination approach to overcome drug resistance and treat tuberculosis. 

<br/>
### III. Which is most prevalent? 
![Topic-Mentions](/assets/images/breast_cancer/topic_mention.png){: .img-container}
Topic 11 is mentioned far more frequently than any other topic. This topic is characterized by conventional treatment methods, namely **chemotherapy** ("agent", "combination") and **hormonal therapy** ("Tamoxifen", "aromatase-inhibitors"). 

The next most frequent topic is Topic 1, characterized by **metastasis** and **immunotherapy**.

<br/>
### IV. What is the trend? 
![Topic-Trend](/assets/images/breast_cancer/topic_trend_rel.png){: .img-container}
Topic 6, characterized by **Triple Negative Breast Cancer (TNBC)**, had the most growth from 1997 to 2017, at +48%. The first mention of TNBC is said to be [October 2005](https://www.nejm.org/doi/full/10.1056/nejmra1001389). TNBC is clinically important because patients with TNBC do not express genes for three key [hormonal receptors](https://en.wikipedia.org/wiki/Triple-negative_breast_cancer), making them [difficult to treat](https://www.sciencedirect.com/science/article/pii/S1470204507700748).

Topic 2, characterized by **nutrition**, had the most negative growth, at -48%. 

This graph also lets us see that Topic 11 is discussed in more than 90% of breast cancer review papers. 

These results were based on a minimum topic contribution of 10%. In the LDA model, every paper is characterized by a mixture of several topics adding up to 100%. 

<br/>
## Conclusion
The purpose of this project was to develop a method for **quickly understanding the landscape of breast cancer research**. This was due to the "cold-start" problem: people without prior knowledge of the field or other subfields do not know which medical terms they could use to begin researching. 

Based on the results from topic modeling, the landscape of breast cancer research can be characterized by the following traits:
1. **More than 90%** of papers discuss conventional treatment methods, namely chemotherapy and hormonal therapy. 
2. **Triple Negative Breast Cancer** is an issue that grew more prominent the last 20 years.
3. Many prominent medical terms are implicated with **chemotherapy resistance**. This problem - the inefficacy of chemotherapy due to drug resistance -- could be a major concern for many researchers. 

It should be noted that this method simply looked for occurrences of words. Thus this method would not only be inadequate in answering most questions in the field, but is also flawed in its ability to generate topics. 

One major factor is the quality and amount of prior preprocessing. I found that the removal of additional biomedical terms from the data skewed the results significantly. 

## Additional Links
* The code for this project is posted [here](https://github.com/edmondchensj/cancer-research).
* A more detailed, interative visualization of the topic model can be found [here](/assets/images/breast_cancer/topic_model.html). This was made using [pyLDAvis](https://github.com/bmabey/pyLDAvis). 
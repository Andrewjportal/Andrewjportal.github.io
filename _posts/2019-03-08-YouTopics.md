---
layout: post
title: YouTopics
---
***
## Overview
YouTopics allows the user to quickly analyze the relevance of a youtube video. Given a user-generated list of keywords representing a topic, YouTopics measures the similarity between the user's topic and a youtube video's topic.

### Motivation
YouTube has a lot information that can be analyzed with NLP thanks to transcripts and auto generated captions. Despite its popularity, I was unable to find example projects working with this information and so it appears youtube is an untouch trove of human communication waiting to be processed.

### Final Product
 ![Image test]({{ site.url }}/images/youtopics.png)
 YouTopics was built to be a web app, but the word embeddings file is too large to be hosted by services like Heroku and Google's app engine. I can host it by opening up an aws server. If I end up doing so a link will posted here.


### Approach
My idea is to extract a summary of the transcripts or captions and then
provide an appropriate topic label. The go to topic models are LDA - and SVD -.

#### LDA:
> LDA takes a collection of documents and with an assumed number of topics, assumed distribution of topics across documents and assumed distribution of words across topics, the model will output, based on probability, a collection of words representing the assumed topics. Intuitively, given the high variance of topics and distributions across billions of videos, LDA won't generalize well with so many assumptions.

#### SVD
> I actually really like SVD, which is also known as the fundamental theorem of linear algebra. It's the base technique for many analytical methods. When applied to NLP it is often referred to as LSA-Latent Semantic Analysis. SVD takes a matrix, in this case a term by document matrix, and decomposes it into three matrices, a term by topics, topic by topic and topic by doc matrix. Like LDA, I fear the topics extracted will not be generally applicable.

My goal is to analyze individual videos, so I will need a unique approach from the LDA / SVD muti-doc one.
Considering the outcome of LDA and SVD is just a list of keywords representing a topic and the python library Gensim has a built in TextRank algorithm that extracts keywords for a given document. I can use these keywords to represent topics and I can then assign a label based on the keywords similarity with pre-established topic keywords. I can use pre-trained word vectors to compute this similarity.

#### Word Vectors
> Word2Vec and GLoVe word embedding map words to vector space where the distance between words is related to semantic similarity. The semantic similarity is computed with the Word2Vec by taking the parameters from a neural network prediction the surrounding words. GloVe uses a more direct approach, GloVe's objective is to predict the log probability of word occurrence across the whole corpus. GloVe's approach is more complicate, but produces generally better results than Word2Vec. Since, I am going to use Stanford's pretrained model the complexity is not a concern, and so I will GloVe embeddings for the performance.

I have determined a method to extract keywords and compare them. The last step is to extract a summary.
NLP summary methods are divided into extractive and abstractive methods. Abstraction uses a lot of compute power to make new summary sentences. I prefer extracting important sentences from the document. It's effective and conveys the same meaning.This is subjective but objective evaluation of NLP is a whole other problem. Gensim has a inbuilt summarizer based on BM25 along with TextRank.

#### TextRank
> Is the same algorithm as PageRank, assigns rank based on unique number of links. In TextRank, links are words in immediate proximity.

#### BM25
> Ranks sentences based on inverse frequency and frequency of keywords from TextRank. BM25
is still obscure to me but it seems intuitively linked to the idea information entropy. A sentence value is based not just on frequency of keywords, but the information value the keywords have in the sentence. Regardless, the results pass the eye test.

The next step is to extract the transcripts from youtube. This can be done through the youtube API. There is always a kind soul in the python communities who will implement a python version of popular API's. In this case I found PyTube.

#### PyTube
> Works great. Thanks, stranger.[Link to the repo](https://github.com/nficano/pytube)

### Future Work 
Add a database storage function for user defined topics. Then apply
database of topics for more robust topic analysis. Add multi-topic
analysis functionality.



---
layout: post
title: YouTopics
---
***
## Overview
YouTopics allows the user to quickly analyze the relevance of a YouTube video. Given a user-generated list of keywords representing a topic, YouTopics measures the similarity between the user’s topic and a youtube video’s topic.


### Motivation
YouTube has a lot information that can be analyzed with natural language processing thanks to transcripts and auto generated captions. Despite its popularity, I was unable to find example projects working with this information and so it appears youtube is a largely untouched trove of human communication waiting to be processed.

### Final Product
 ![Image test]({{ site.url }}/images/youtopics.png)
 YouTopics was built to be a web app, but the word embeddings file is too large to be hosted by services like Heroku and Google's app engine. I can host it by opening up an aws server. If I end up doing so a link will posted here.


### Approach
My idea is to extract a summary of the transcripts or captions and then provide an appropriate topic label. The go to topic models are LDA - and SVD -.

#### LDA:
> LDA takes a collection of documents and with an assumed number of topics, assumed distribution of topics across documents and assumed distribution of words across topics, generates, based on probability, a collection of words representing topics. Intuitively, given the high variance of topics and distributions across billions of videos, LDA won't generalize well with so many assumptions.

#### SVD
> I actually really like SVD, which is also known as the fundamental theorem of linear algebra. It's the base technique for many analytical methods. When applied to NLP it is often referred to as LSA-Latent Semantic Analysis. SVD takes a matrix, in this case a term by document matrix, and decomposes it into three matrices, a term by topics,topic by topic and topic by doc matrix. Once again, however, the number of topics are guessed or assumed.

Both methods take a sample of documents to generate word lists representing topics. Finding a sample large enough to generalize these models to all of YouTube would be impractical. So, I came up with a different approach.



Considering the outcome of LDA and SVD is just a list of keywords representing a topic and the python library Gensim has a built in TextRank algorithm that extracts keywords for a given document. I used these keywords to represent topics and I then assign a label based on the keywords similarity with pre-established topic keywords. I  used pre-trained word vectors to compute this similarity.

#### Word Vectors
> Word2Vec and GLoVe word embedding map words to vector space where the distance between words relates semantic similarity. With Word2Vec, the semantic similarity is computed by taking the parameters from a neural network prediction of surrounding words. GloVe uses a more direct approach, GloVe's objective is to predict the log probability of word occurrence across the whole corpus. GloVe's approach produces generally better results than Word2Vec, though it is more difficult to trian. Since, I am going to using pretrained models anyway, I decided to use GloVe.

I have determined a method to extract keywords and compare them. The last step is to extract a summary.
NLP summary methods are divided into extractive and abstractive methods. Abstraction uses a lot of compute power to make new summary sentences.I prefer extraction, which from summaries by just pulling important sentences from the document. It effectively convay the same meaning while using less computing power. Gensim has an inbuilt summarizer based on BM25 along with TextRank.


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



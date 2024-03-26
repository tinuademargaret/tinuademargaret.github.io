---
layout: post
title: An overview of language model (Part 1)
date: 2022-11-14 10:14:00-0400
description: Where we started
tags: AI
categories:
giscus_comments: false
featured: true
related_posts: false
---

Language generation by humans is a sequential process. In acts of communication like speaking or writing, language is generated one word at
a time and a preceding word or sequence of words would determine the next word depending on the rules of the language in use and the words
one is familiar with in that language. A language model mimics this process by predicting the next word given a previous word or sequence of
words. It makes this prediction by computing the probability distribution over a vocabulary of words(all the words the model is aware of)
which shows how likely each word in the vocabulary would be the next word.

For example, given the input sequence "I love playing", a trained language model is able to predict that the next word is most likely to be
"football" rather than "bread".

Language modelling is a fundamental task in NLP and it is used as the basis of other NLP tasks such as machine translation,
text summarization etc. Notice that each of these tasks involves the generation of some text given some input context.

Prior to the adoption of the use of deep learning techniques in NLP tasks, language modelling was done using statistical modelling with the
dominant approach being the n-gram LM.


## n-gram Language Models

A sequence of n-words is what is said to be an n-gram. In a 1-gram model, the sentence "I am a girl" is chunked into sequences of 1 word
as *(I) (am) (a) (girl)* and each chunk is known as a *unigram*, likewise in a 2-gram model, the same sentence is chunked as
*(I am) (am a) (a girl)* and each chunk is known as a *bigram*.

An n-gram LM calculates the probability distribution using the Markov assumption that the next word in a
sequence only depends on the previous n-1 words. If n is 2 then the probability distribution is estimated on a one-word history. A number of successful n-gram models made use of a value of n=5.

n-gram models performed well with small values of n but the performance decreased with larger values of n. This is not far-fetched as
the probability of finding long sequences in a text corpus is low and this lowers the chances of the sequence being included as part of
the training data. Also with larger values of n, the model becomes increasingly complex. The size of the context being modelled in an
n-gram is a limitation to the model as it cannot capture long-range dependencies which are often seen in natural language.

The n-gram model also fails to capture semantic relationships between contexts. For example, we can tell that two contexts like
"The Tempest was written by William Shakespeare" and "The author of The Tempest is William Shakespeare" mean the same, an n-gram model
fails to recognize and capture this similarity.

The limitations of the n-gram models have been greatly improved upon by the adoption of deep learning techniques in performing
language modelling tasks. These kinds of language models are called Neural Language Models.


## Neural Language Models

In this approach, a feature vector usually one dimensional, is associated with each word in a vocabulary,
the probability function which is used in n-gram LMs is replaced by a neural network and is now expressed as the
conditional probability of predicting the next word given the previous ones. As with neural networks in general, the parameters of the
function is tuned in an iterative manner with the objective of minimizing the prediction error of the network.

This approach obtained significantly better results compared to the best of the n-gram models. The neural network learns similar words
and represents them with similar vector features which makes the language model generalize better. The success of neural language models
naturally gives way to more complex neural language models with better performance which we would look at "in my next post". Irrespective of
the architecture of these models, there are some key components that are common to them. These components include:

### Embedding Layer

So far we have talked about the input to an LM as a sequence of words and we have talked about calculating the probability of the next word
given a sequence of words, but we have not explained how we map words into numerical data to enable the performance of numerical computations.

Usually, the mapping of words to numerical data known as *Word embeddings* is done via *one hot encoding*. In one hot encoding,
every word in a vocabulary is represented as a vector of size |V| which is the length of the vocabulary(i.e the total number of words that needs to be encoded).
The position of the particular word being represented is set to a value of 1 while every other word's position in the vocabulary is set to 0. This data quickly becomes
large as the size of the vocabulary increases. Also when this data is fed into a neural network a lot of computation goes on for data
that is mostly zeros which is quite inefficient.

An improvement to this was the addition of an embedding layer (the feature vector associated with each word described above) to the
neural network. The embedding layer in this case is randomly initialised and jointly trained with the rest of the network layers.
The matrix formed by the feature vectors then forms an embedding lookup table |E| of length |V|.

The [word2vec paper](https://arxiv.org/pdf/1301.3781.pdf) proposed a word embedding model, which pre-trains word embeddings that are used to initialise the word embedding layer
in an LM. This further improved the performance of neural language models on a range of NLP tasks.


### Softmax Layer

Language modelling can be seen as a multiclass classification task with the number of classes equal to the length of the vocabulary |V|.
To make the output of the LM a probability distribution over a vocabulary of words, the softmax layer has weights similar to the embedding
layer and the softmax activation function is used to transform the output into a probability distribution.


In a continuation post we would look at different Neural Model Architectures including the recent and popular Transformers.

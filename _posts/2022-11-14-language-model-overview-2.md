---
layout: post
title: An overview of language model (Part 2)
date: 2022-11-14 10:14:00-0400
description: Where we are now
tags: AI
categories:
giscus_comments: false
featured: false
related_posts: false
---

In the previous post we looked at how languge modelling was done with n-gram models and some key components of neural language models namely the embedding layer and the softmax layer.


While the Embedding layer and the softmax layer are common to LMs in general, the network layer varies and is a distinguishing factor in
the capabilities of LMs. In this post, we'll look at two major architectures namely Recurrent Neural Networks (RNN) and Transformer Networks.

## Reccurent Neural Networks

The major difference between a vanilla Neural Network commonly referred to as a Feed Forward Neural Network (FFNN) and an RNN, is the
incorporation of memory (the output of hidden layer neurons) as an additional input to the network during the next step training on
sequential data. The layers in an RNN contain RNN cells as opposed to neurons seen in FFNNs. The output of an RNN cell at time *t* depends
on the current inputs, their weights, and also the output of the hidden layer in the previous time step.

Because of the additional memory component, the error of the output of the model is usually backpropagated through previous timesteps and if
backpropagation is done for more than approximately ten timesteps the gradient can become either very small leading to a vanishing gradient
problem or grow uncontrollably leading to an exploding gradient problem. The vanishing gradient problem especially hinders the capability of
the model to capture information several time steps back.

Long Short-Term Memory Networks (LSTMs) and Gated Recurrent Networks (GRNs) are variants of RNNs proposed to solve the vanishing gradient problem.
Christopher Olah gives a good breakdown on RNNs [here](https://colah.github.io/posts/2015-08-Understanding-LSTMs/).

## Sequence-to-Sequence models

The encoder-decoder sequence-to-sequence model was originally proposed to overcome the limitations of performing sequence-to-sequence mapping with neural networks
especially in machine translation tasks.

For an RNN to process sequential data the dimensions of the input and output have to be known and fixed, but in many cases,
problems are best expressed as sequences whose lengths are not previously known, or whose input sequence length varies from the output
sequence length such as in machine translation.

In a vanilla sequence-to-sequence model, the encoder and decoder is usually any of the RNNs mentioned above. The encoder takes an entire input sentence and maps it to a fixed-sized vector.
This vector is then fed into the decoder which then maps the vector to a target sequence.

Let's talk a bit about the attention mechanism.

### Attention mechanism
Attention mechanism in machine learning originated from the observation that humans process sensory information in a sequential manner,
paying special attention to a part of the whole information at a time. Researchers have used the Attention mechanism to replicate this
behaviour in neural networks enabling them to focus on a subset of input information at a time. For example, in image captioning,
a computer vision network can focus on different parts of the image at a time to produce a more accurate caption.

Attention between two sequences was initially introduced in a neural machine translation task. Before the use of attention, when performing machine translation tasks using sequence-to-sequence models, as we have described above, the entire
input sequence is passed as a vector from the encoder to the decoder. Still, with attention, the encoder passes information about
each word it processes to the decoder, but the decoder can focus on words as they become important.

To induce attention, the decoder scores the set of vectors (each associated with a word in the input sequence) from the encoder at each time
step. It applies a softmax function to create an attention distribution. Each vector is then multiplied by its softmax score, drowning out
vectors with lower scores and amplifying vectors with higher scores. There is a nice visualization of sequence to sequence models by Jay Alammar [here](https://jalammar.github.io/visualizing-neural-machine-translation-mechanics-of-seq2seq-models-with-attention/)

The attention mechanism introduced in vanilla sequence-to-sequence models made it compelling, but there was still an issue of
computational efficiency since computation was done sequentially using recurrent models. This led to a proposal for the Transformer to reduce the
sequential computation in sequential modelling tasks.


## Transformers

The original proposed transformer is composed of an encoder-decoder network. It removes which the recurrent connections seen in sequence to sequence models, and uses the
attention mechanism to model information across long sequences. The function of the encoder and decoder in a transformer network remains
just the same as in a vanilla sequence-to-sequence model but the transformer network uses not just an encoder and a decoder but a stack of
encoders and decoders. Each encoder and decoder can be seen as a layer of the transformer network. Each layer of the decoder is made up of
two sub-layers, a multi-head attention layer and a feed-forward layer which is essentially a feed-forward neural network.
A Layer normalisation is also included around the sub-layers which helps to improve the training speed of the model.
In addition to the sub-layers seen in the encoder, the decoder has a third sub-layer for performing multi-head attention over the output of
the encoder stack.

Instead of relying on the output of the previous state to encode information about the last step time in the current time step,
attention is used in transformers to encode information about relevant words into a word currently being processed.
The process to achieve this is similar to the one described above between an encoder and a decoder in a vanilla sequence-to-sequence
model with attention. In this case, we score each word in an input sentence against a word currently being processed, and this score
determines how much focus should be placed on the other words in the input sentence as one is currently encoded. A softmax function is
then applied to the scores. A value vector for each word is then multiplied by the softmax score for each word, again drowning out vectors
with lower scores and amplifying vectors with higher scores. The vectors are then summed up to produce the output of the attention layer in
an encoder and fed into the feed-forward layer. This calculation is done multiple times to create \emph{multi-headed} attention and
improves the performance of the attention layer.


### Positional Embeddings
Transformers need a way to represent the position of words in a sequence of input since they do not have recurrent connections.
To address this, the proposed model adds a vector to each input word embedding called a position embedding that helps it know the position
of each word and the distance from each word.
The positional embedding follows a specific pattern and can either be learned or determined by a pre-defined function.
The sinusoid function was used in the original implementation.


The Transformers, since its inception, has been highly adopted as a model architecture for LMs. Some popular based Transformer
based language models include BERT, RETRO, GPT-2 and its successors.

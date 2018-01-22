---
layout: post
title: Of sNNarXiv titles
series: Physics
date: '2018-01-10 8:00:00'
---
# Work in progress!


This is a follow up on the [sNNarXiv](https://dlvp.github.io/Physics-snnarxiv/) project. I closed that post with a wishlist. One thing I thought would have been cool was some kind of algorithm to generate titles conditioning on an abstract. This is typically what happens in real life, so why not. Turns out that the simplest implementation of such an algorithm is quite easy and well known.

The basic idea is the same one that is used in neural network models for language translation. There are two Recurrent Neural Networks. One, the `encoder`, read the abstract word by word and encapsulate the abstract in a hidden state vector `h`. The second part of the network, the `decoder`, is initialized with the hidden state vector `h`, and get as the first input some special token signalling the beginning of the sentence, in this case `<GO>`. At each time step the decoder is supposed to output a word of the target sentence (a title in my case) and this word is used to be the input at the next time step. Figure 1 shows it pictorially.


















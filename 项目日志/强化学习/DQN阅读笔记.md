---
title: DQN阅读笔记
renderNumberedHeading: true
grammar_cjkRuby: true
---

# Overview
## target
Learning to control agents directly from high-dimensional sensory inputs
## main idea
These methods utilise a range of neural network architectures, including convolutional networks, multilayer perceptrons, restricted Boltzmann machines and recurrent neural networks, and have ex-ploited both supervised and unsupervised learning.
## challenge
- Firstly, most successful deep learning applications to date have required large amounts of hand-labelled training data.
- RL algorithms, on the other hand, must be able to learn from a scalar reward signal that is frequently sparse, noisy and delayed.
- Another issue is that most deep learning algorithms assume the data samples to be independent, while in reinforcement learning one typically encounters sequences of highly correlated states.
- Furthermore, in RL the data distribu-tion changes as the algorithm learns new behaviours, which can be problematic for deep learning methods that assume a fixed underlying distribution.

## Overcome
- This paper demonstrates that a convolutional neural network can overcome these challenges to learn successful control policies from raw video data in complex RL environments.
- The network is trained with a variant of the Q-learning [26] algorithm, with stochastic gradient descent to update the weights.
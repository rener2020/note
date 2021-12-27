---
title: DQN阅读笔记
renderNumberedHeading: true
grammar_cjkRuby: true
---

# Introduction
## target
Learning to control agents directly from high-dimensional sensory inputs
## main idea
These methods utilise a range of neural network architectures, including convolutional networks, multilayer perceptrons, restricted Boltzmann machines and recurrent neural networks, and have ex-ploited both supervised and unsupervised learning.
## challenge
- Firstly, most successful deep learning applications to date have **required large amounts of hand-labelled training data**.
- RL algorithms, on the other hand, must be able to learn from a scalar reward signal that is frequently sparse, noisy and **delayed**.
- Another issue is that **most deep learning algorithms assume the data samples to be independent**, while in reinforcement learning one typically encounters sequences of highly correlated states.
- Furthermore, in RL the data distribu-tion changes as the algorithm learns new behaviours, which can be problematic for deep learning methods that assume a fixed underlying distribution.

## Overcome
- This paper demonstrates that a convolutional neural network can overcome these challenges to learn successful control policies from raw video data in complex RL environments.
- The network is trained with a variant of the **Q-learning [26] algorithm**, with stochastic gradient descent to update the weights.
- To alleviate the problems of correlated data and non-stationary distributions, we use an <font color="red">**experience replay mechanism**</font> [13] which randomly samples previous transitions, and thereby smooths the training distribution over many past behaviors.

# Background


$\infty$

# DQN algorithm
## cumulative reward
$$R_{t0}=\sum_{t=t_0}^\infty\gamma^{t-t_0}r_t$$
- $R_{t_0}$  is also known as the return
-  $\gamma$, should be a constant between 0 and 1

The main idea behind Q-learning is that if we had a function $Q^*$
$$Q^*:State\times{}Action$$
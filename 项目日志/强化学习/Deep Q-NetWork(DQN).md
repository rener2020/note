---
title: Deep Q-NetWork(DQN)
renderNumberedHeading: true
grammar_cjkRuby: true
---

# Approximate the Q Function
**goal**: win the game ($\approx$ maximize the total reward.)
**Question**: If we know $Q^*(s,a)$, what is the best action?
- obviously, the best action is $a^*=\underset{a}{argmax}Q^*(s,a)$ 

**Challenge**: We do not know $Q^*(s,a)$.

- solution: DQN
- Use neural network **Q(s,a;w)** to approximate $Q^*(s,a)$

## DQN
- input shape: size of the screenshot.
- Output shape: dimension of action space

![enter description here](./images/1640326445790.png)

## Temporal Difference (TD) Learning
![enter description here](./images/1640326719232.png)

**Example**
- I want to drive from N to A
- Model $Q(w)$ estimates the time cost, e.g., 1000 minutes

**Question：how to update the model**
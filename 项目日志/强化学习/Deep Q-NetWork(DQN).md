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
1. Gradient descent

- Can i update before finishing the trip?
	- TD target (more reliable)
	- TD error
- Can i get a better $w$ as soon as I arrived DC? 

## TD Learning for DQN
$$T_{N\to{}A}\approx{}T_{N\to{}D}+T_{D\to{}A}$$

In deep reforcement learning:
$$Q(s_t,a_t;w)\approx{}r_t+\gamma\cdot{}Q(s_{t+1},a_{t+1};w)$$

identify:$U_t=R_t+\gamma$
- DQN's output, $Q(s_t,a_t;w)$, is estimate of $\mathbb{E}[U_t]$
- DQN's output, $Q(s_{t+1},a_{t+1};w)$, is estimate of $\mathbb{E}[U_{t+1}]$
- Thus, $Q(s_t,a_t;w)\approx{}\mathbb{E}[R_t + \gamma\cdot{}Q(S_{t+1},A_{t+1};w)]$
- $Q(s_t,a_t;w)\approx{}r_t + \gamma\cdot{}Q(S_{t+1},A_{t+1};w)$

![enter description here](./images/1640331622567.png)

![enter description here](./images/1640331604386.png)

## Train DQN using TD learning
- prediction;$Q(s_t,a_t;w_t)$
- TD target: $y_t=r_t+\gamma\cdot{}Q(s_{t+1},a_{t+1};w_t)=r_t+\gamma\cdot{}\underset{a}{max}Q(s_{t+1,a;w_t})$
- Loss: $L_t=\frac{1}{2}[Q(s_t,a_t;w)-y_t]^2$
- Gradient descent

## Value-Based Reforcement Learning
Definition: Optimal action-value function
- $Q^*(s_t,a_t)=\underset{\pi}{max}\mathbb{E}[U_t|S_t=s_t,A_t=a_t]$.

DQN: Approximate $Q^*(s,a)$ using a neural network
- $Q(s,a;w)$ is a neural network parameterized by $w$
- Input: observed state $s$
- Output: scores for every action $a\in{}\mathcal{A}$

## Temporal Difference (TD) Learning
Algorithm: One iteration of TD learning
1. Observe state $S_t=s_t$ and action $A_t=a_t$
2. Predict the value: $q_t=Q(s_t,a_t;w_t)$.
3. Differentiate the value network: d_t
4. Environment provides new state $s_{t+1}$ and reward $r_t$.
5. Compute TD target: $y_t=r_t+\gamma\cdot{}\underset{a}{max}Q(s_{t+1},a;w_t)$
6. Gradient descent
	
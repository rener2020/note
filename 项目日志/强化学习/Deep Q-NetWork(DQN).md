---
title: Deep Q-NetWork(DQN)
renderNumberedHeading: true
grammar_cjkRuby: true
---

# Approximate the Q Function
**goal**: win the game ($\approx$ maximize the total reward.)
**Question**: If we know $Q^*(s,a)$, what is the best action?
- obviously, the best action is $a^*=\underset{a}{argmax}Q^*(s,a)$ 
Challenge: We do not know $Q^*(s,a)$.
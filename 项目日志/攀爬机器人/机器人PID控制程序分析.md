---
title: 2021-07-11未命名文件 
tags: 新建,模板,小书匠
renderNumberedHeading: true
grammar_cjkRuby: true
---

$p_{error}= target - current$
$i_{error}=i_{error\_last} + p_{error}$
$d_{error}=(p_{error} - p_{error\_last})/t_{during}$

$p_{term} = p_{gain} * p_{error}$
$i_{term} = i_{gain} * i_{error}$
$i_{term}=MAX(i_{min}, MIN( i_{term},i_{max})$
$d_{term} = d_{gain} * d_{error}$
$term=p_{term}+i_{term}+d_{term}$

$force=term$

$REVOLUTE$
$M=α*{I}=force* L$
$I=m*{r}^{2}$
$\to \alpha=\frac{force* L }{m* r^2}$

$CONTINUOUS$
$force=ma$
$a=\frac{force}{m}$
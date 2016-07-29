---
layout: post
title: Different university rankings... how to combine them?
date: 2016-07-28
description: Given a list of rankings, how do we combine them in a way that makes sense?
---

The dataset is from [kaggle](https://www.kaggle.com/mylesoneill/world-university-rankings)
which consists of three different lists of
university rankings along with some economic indicators. The lists are from 
[Times](https://www.timeshighereducation.com/world-university-rankings), 
[Shanghai](http://www.shanghairanking.com/), and [Century World](http://cwur.org/).

Combining rankings is an interesting problem from 
[voting theory](https://en.wikipedia.org/wiki/Voting_system) which sounds easy but it is a
non-trivial task. As a former economics student, this brings into mind __Arrow's impossibility 
theorem__ which states that you cannot design a voting system that satisfy three criteria: 
(1) 
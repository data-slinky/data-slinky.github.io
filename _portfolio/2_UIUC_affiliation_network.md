---
layout: post
title: UIUC Affiliation Network
description: Interactive network viz of UIUC employees
img: /img/uiuc_net.png
---

Check out the visualization [here](https://data-slinky.github.io/UIUC_network/).

# Motivation

I got the idea for this project when a friend was looking for a thesis advisor. Since he 
was in ECE, he had to find an advisor within the department or a faculty affiliate. There 
isn't a great way to find who is affiliated with who besides going to the department
website and looking at people's bio so I thought making a network visualization would be 
useful (and fun) thing to do.

# How I did it

You can find the code [here](https://github.com/data-slinky/UIUC_network). A tutorial of the 
process is given in a series of posts:

1. [Scraping the data]({% post_url 2016-07-22-uiuc-network-p1 %})
2. [Creating the network]({% post_url 2016-07-25-uiuc-network-p2 %})
3. [Visualizing]({% post_url 2016-07-27-uiuc-network-p3 %})
4. [Visualizing 2.0]({% post_url 2016-08-27-uiuc-network-p4 %})

# Things to note from the d3 viz

* Size of departmental nodes is determined by out-degree
* The distance between the department nodes is based on number of affiliated people (e.g.,
ECE is really close to CSL since lots of people are in both)
* Some departments have employees with no affiliations so they are floating by themselves 
endlessly in space (how lonely)
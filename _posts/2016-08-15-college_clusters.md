---
layout: post
title: "What do the top universities have in common? Impactful research and... proximity?"
date: 2016-08-15
description: Unsupervised learning on the college rankings data.
tags: [R, machine_learning, clustering]
---

Part of project: [College Rankings](http://data-slinky.com/project/3_College_rankings/)

# Introduction

We use the latest [Times Higher Education (THE) World University Rankings](https://www.timeshighereducation.com/world-university-rankings/2016/world-ranking)
data for this analysis. THE ranks colleges based on five categories: (1) Teaching (30%), (2) Research (30%),
(3) Citations (30%), (4) International outlook (7.5%), and (5) Income (2.5%). Each colleges have a
1-100 score for each categories and the final score is simply the sum of each categories 
according to the weights. The colleges are then assigned a rank based on their overall score.

While high-ranking colleges will obviously have high scores in each category, this does not 
necessarily mean the distribution of scores are the same. For example, School A might score 
very high in citations but have a low score for Teaching than School B. While they both may
be in the Top 50, they maybe more different than they are alike. Let us perform 
cluster analysis to see if we can spot any groupings.

<figure>
 <a href="https://raw.githubusercontent.com/data-slinky/college_rankings/master/Clustering/heatmap.png
" data-lightbox="appfoundry_image_set" data-title="Source: Times World University 2016. Colleges are ordered. Note: Columbia does not have an income score.">
  <img src="https://raw.githubusercontent.com/data-slinky/college_rankings/master/Clustering/heatmap.png" alt="Heatmap" style="max-width:80%;"/>
</a>
 <div class="col caption">Click image to view full-size. </div>
 </figure>

The first thing I did was to examine the __heatmap__ based on __hierarchical clustering__. While the 
results of hierarchical clustering is subjective, it can give some interesting insights. In 
this case, the rows are ordered of the Top 50 schools and the shaded tiles corresponds to 
how they fare for each category.

Notice that the top ~25 schools scored very well for citations which implies that the research
from these institutions are very, very impactful. The quality of teaching is very high for 
the top 5 schools but gradually lower as the rankings go on. Same goes with the research 
category. The international and income categories are spotty which makes sense since they 
make up a small percentage of the overall score. I also plotted the country of origin for 
each school but it was pretty obvious that the top schools are predominantly American and European.

# Partitional Clustering

From the heatmap we see that impact matters a lot. While it has the same weight
as teaching and research, it is a much stronger commonality between the very best
institutions. Let us apply partition clustering using k-means to be more in-depth. Before 
doing so, it is worth it to figure out the optimal number of clusters. Two popular ways of 
doing so is the __Elbow Method__ and __Average Silhouette Width__. In the figures below, we 
see that both method points to k=3.

<figure>
 <a href="https://raw.githubusercontent.com/data-slinky/college_rankings/master/Clustering/optimal_k.png
" data-lightbox="appfoundry_image_set" data-title="Finding the optimal number of clusters.">
  <img src="https://raw.githubusercontent.com/data-slinky/college_rankings/master/Clustering/optimal_k.png" alt="Optimal k" style="max-width:90%;"/>
</a>
 <div class="col caption">Click image to view full-size. </div>
 </figure>

We now perform k-means with three clusters. I plotted the pairs plot for each category in 
the figure below. The upper triangular contains the Pearson's correlation between the two 
categories and the lower triangular are the clusters.

<figure>
 <a href="https://raw.githubusercontent.com/data-slinky/college_rankings/master/Clustering/pairs_plot.png
" data-lightbox="appfoundry_image_set" data-title="Pair plots of each categories with clusters. Upper triangular is Pearson's correlation coefficients.">
<img src="https://raw.githubusercontent.com/data-slinky/college_rankings/master/Clustering/pairs_plot.png" alt="Optimal k" style="max-width:90%;"/>
</a>
 <div class="col caption">Click image to view full-size. </div>
 </figure>
 
While it is obvious that there should be a positive correlation coefficient for each categories,
one thing that stood out to me is the high correlation between research and teaching. For 
research universities, it is common that teaching duties comes second to research for 
faculty. It turns out that at the top universities, these two things go hand in hand. The 
clustering result is very unclear as there are a lot of overlaps between universities. 
There are clearly 3 clusters for international reputation versus income however. There are 
clearly some universities with high income and and international reputation but also some 
with low international reputation and low income.

Now, which of the top universities are most similar to one another? We examine the two 
components of the data that explains the most variations. The cluster plot is given below 
which clearly shows 3 clusters of universities.

<figure>
 <a href="https://raw.githubusercontent.com/data-slinky/college_rankings/master/Clustering/cluster_plot.png
" data-lightbox="appfoundry_image_set" data-title="Cluster plot of the colleges.">
<img src="https://raw.githubusercontent.com/data-slinky/college_rankings/master/Clustering/cluster_plot.png" alt="Optimal k" style="max-width:90%;"/>
</a>
 <div class="col caption">Click image to view full-size. </div>
 </figure>

I was not really surprised that my university, UIUC, is close to Northwestern,
UW-Madison and Michigan since the Big Ten schools tends to be ranked very close to one 
another. The biggest insight I gather from the data is that __colleges that are close to 
one another are ranked very similarly__ in terms of the five categories. Aside from the Big Ten
schools, notice that the UK schools (Edinburgh, Kings College, LSE) as well as the Asian
schools (Tsinghua, Peking, Tokyo) are close together. Of course, the elite universities will 
be in a class of their own as see the giant clutter of Harvard, Yale, Princeton, OxBridge.
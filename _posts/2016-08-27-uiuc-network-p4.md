---
layout: post
title: UIUC interactive network (p4)
date: 2016-08-27
description: The UIUC universe... minus the students and the interactive part ᕦ(ò_óˇ)ᕤ
tags: [network, Visualization, software]
---

Part of project: [UIUC Affiliation Network](http://data-slinky.com/project/2_UIUC_affiliation_network/)

In my previous post [post 3]({% post_url 2016-07-27-uiuc-network-p3 %}), I mentioned that I will
only sample part of the 6,474 nodes and 8,271 edges of the network since d3.js was super slow at 
rendering the entire thing. While visualizing such large network is totally doable relatively
quickly using Python or R, I wanted to explore some softwares specifically designed for the task.

I played around with two open-source software: (1) [Gephi](https://gephi.org/) and (2) 
[Cytoscape](http://www.cytoscape.org/). There are pros and cons for both. I found the UI of 
Gephi to be very well-designed and intuitive as option is where you expect it to be. However, it 
seems very slow at rendering my network as the preview function with even forced direct layout took 
over 5 minutes (on a decent machine with 12mb of memory). I found Cytoscape to be the total opposite.
It was very fast but the UI was not as intuitive. Embarassingly, it took me about an hour and a few
YouTube videos to learn how to do simple things such as setting scaling node sizes and different node 
colors.

As it was originally an application for biological research, Cytoscape allows you to download some 
bio-databases to play around with which was very fun. The strong community support provided an ample
number of layout types to experiment with. I eventually went with the strong-cluster layout from 
[Allegro](http://apps.cytoscape.org/apps/allegrolayout).

<figure>
 <a href="https://raw.githubusercontent.com/data-slinky/UIUC_network/gh-pages/Visualization.png" data-lightbox="appfoundry_image_set" data-title="Source: 
 UIUC Universe... minus the students.">
  <img src="https://raw.githubusercontent.com/data-slinky/UIUC_network/gh-pages/Visualization.png" alt="UIUC Universe" style="max-width:100%;"/>
</a>
 <div class="col caption">Click image to view full-size.</div>
 </figure>
 
The orange dots correspond to people while the green dots correspond to the departments. Again, I 
made the size of the department nodes scale by it's out-degree. Honestly, we cannot tell much by just
looking at a screenshot so you probably should load the _.cys_ file from the repository to Cytoscape
to gather more information.
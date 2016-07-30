---
layout: post
title: UIUC interactive network (p3)
date: 2016-07-27
description: Interactive visualization with d3.js (oh, so pretty!)
---

We can now visualize the network! In case you'd missed it, check out 
[post 1]({% post_url 2016-07-22-uiuc-network-p1 %}) for obtaining the data and 
[post 2]({% post_url 2016-07-25-uiuc-network-p2 %}) on setting up the network. Before we 
visualize it, a quick disclaimer.

With 6,474 nodes and 8,271 edges, the network is pretty large. The algorithm which d3.js 
uses to render a forced layout graph is _O(n log n)_ so it might take your browser a bit
of time to render. Also, the visualization will not look pretty since everything is too 
cluttered. Therefore, I suggest taking a sample of the data for this next step. I provided 
all of the departments associated with the engineering college in the Github repository 
as the sample I'm going to use.

# 2.5 Setting up simple server

You should set up a simple server to preview your html file. This is really easy to do with 
Python. Just open up the command line, navigate to your project directory and type the 
following if you're using python3:

```python
python3 -m http.server
```

or the following if you are using python2:

```python
python -m SimpleHTTPServer 8000
```

You should see an ip address with the port number. Open up a browser and navigate to that 
address to see a preview of the html file.

# 3. Visualize

First, make sure you've installed [d3.js](https://d3js.org/). The first part of the code 
is pretty straightforward since I'm setting the colors for the network. The important bit 
is where I set the hover and the mouse pointer-event. We want to be able to see the name 
of the node when our cursor is over it.

```html
.node:hover text {
  display: inline;
}

.cell {
  fill: none;
  pointer-events: all;
}
```

With the d3.js script, we begin by setting up some variables for the visuals and creating a
[force layout graph](https://en.wikipedia.org/wiki/Force-directed_graph_drawing).

```html
var color = d3.scale.category10();

var width = 1200,
    height = 800

var svg = d3.select("body").append("svg")
    .attr("width", width)
    .attr("height", height);

var force = d3.layout.force()
    .gravity(0.4)
    .charge(-120)
    .linkDistance(100)
    .size([width, height]);
```

The variable `color` is essentially an ordinal scale with a range of ten categorical 
colors. The _width_, _height_ and _svg_ is for the size of our visualization (_svg_ is
essentially an xml language to create graphics). The final bit is to create a force layout
object with some specific properties. _gravity_ is the pull of the nodes to a fixed point,
_charge_ determines how much the nodes repel each other, _linkDistance_ determines the 
distance between connected nodes. I suggest playing around with the variables to get a 
better sense of what its doing.

Now, lets read in the json graph file we'd created and pass it into the force object. Note,
we want to throw an error just in case there is a problem with the input.

```html
d3.json("uiucSample.json", function(error, json) {
  if (error) throw error;

  force
      .nodes(json.nodes)
      .links(json.links)
      .start();
```

The next bits of code is self-explanatory in that we set the variables to be what is in 
the data. There are two parts which we should discuss in detail.

```html
  var circle = node.append("circle")
      .attr("r", function(d) { return d.value+5; })
      .style("fill", function (d) { return color(d.group); });

...

  node.on('mouseover', function(d) {
  link.style('stroke', function(l) {
    if (d === l.source || d === l.target)
      return 'red';
    else
      return 'lightgreen';
    });
  });

  node.on('mouseout', function() {
    link.style('stroke', 'lightgreen');
  });
```

The variable _circle_ will draw our node which we want to be in two different colors (one
for department and one for people) as well as different size. Next, we want to highlight 
the edges of the node when we mouse over it. To do so, we have a function which returns 
the color of the edge based on whether it is connected to another node. The rest of the 
code is the standard settings for how the force layout to be drawn.

Check out the visualization [here](https://jknguye2.github.io/UIUC_network/).
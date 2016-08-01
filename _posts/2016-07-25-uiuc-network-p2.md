---
layout: post
title: UIUC interactive network (p2)
date: 2016-07-26
description: Creating a network from the data with NetworkX. Aka, the not so fun step.
---

Part of project: [UIUC Affiliation Network](http://data-slinky.com/project/2_UIUC_affiliation_network/)

In the [previous post]({% post_url 2016-07-22-uiuc-network-p1 %}), we scraped the data from 
the web. If you look in the source code, I've included a little snippet on how to get the
data in a nested dictionary to make it easy to dump to json. Here, I will discuss a much 
easier alternative using the NetworkX library.

# 2. Creating the network

The output file _uiucData.txt_ is essentially an edgelist consisting of two categories of 
nodes, department and people. We will categorize each nodes to one or the other.

```python
import networkx as nx
from networkx.readwrite import json_graph
import json
import pandas as pd

df = pd.read_csv("uiucData.txt", header = None, delimiter = '\t')
df.columns = ['Department', 'People']
department = df.Department.unique()
people = df.People.unique()
```

Using _pandas_, we read the data file which is tab-delimited. We know that the left column 
corresponds to the department and the right correspond to people so it is very simple to 
obtain a unique list of each. Now, we can create the network from the dataframe.

```python
G = nx.from_pandas_dataframe(df, 'Department', 'People')
d = nx.degree(G)
```

Pretty simple, huh? We use `degree()` to obtain the degree of each node because we 
eventually want to scale the size of the nodes based on how connected it is. Now, we need 
to output this network to a json network format to be able to create the visualization. The 
structure we want looks like so:

```json
"nodes": [
	{
	"name": "Orzek, Marvis Ann", 
	"group": "People"
	"id": "Orzek, Marvis Ann"
	}
			...
"links": [
	{
	"source": 0,
	"target": 110
	}
			...
```

Thankfully, this is very easy to do with NetworkX with some minor tweaks to the network.

```python
for n in G:
    G.node[n]['name'] = n
    G.node[n]['value'] = (((d[n] - min(d.values())) * (10 - 1)) / (max(d.values()) - min(d.values()))) + 1
    if n in department:
        G.node[n]['group'] = 'Department'
    else:
        G.node[n]['group'] = 'People'
```
Iterating through the network, we gave the network a name based on its id. We then assign 
a value to the nodes based on its degree. We use max-min scaling because there are some 
nodes with very high degrees and most have only 1.  We also assign the group to the node 
based on the list obtained from the dataframe.

Now you just need to dump the network as a json and viola, you're done!

```python
d = json_graph.node_link_data(G)
json.dump(d, open('uiuc.json','w'))

In the next post, we will visualize the network with d3.js.
```
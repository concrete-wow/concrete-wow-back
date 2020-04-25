---
jupyter:
  jupytext:
    formats: ipynb,md
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.2'
      jupytext_version: 1.3.4
  kernelspec:
    display_name: Python 3
    language: python
    name: python3
---

# Generate a random graph for testing the Dirichlet pagerank

```python
import pandas as pd
import numpy as np

import matplotlib as mpl
import matplotlib.pyplot as plt
%matplotlib inline

import networkx as nx
from networkx.readwrite import json_graph
import json
import random
```

## Creating a graph
Create a random small-world graph

```python
G = nx.connected_watts_strogatz_graph(100, 8, 0.2)
```

```python
nx.info(G)
```

```python
nx.degree_histogram(G)
```

```python
# plt.subplot(121)
# nx.draw(G, with_labels=True, font_weight='bold')
# plt.subplot(122)
nx.draw_spring(G, 
#               nlist=[range(5, 10), range(5)], 
              with_labels=True, font_weight='bold')
```

## Make the graph directed, with the same number of edges.

```python
Gd = G.to_directed()
nx.info(Gd)
```

```python
nx.algorithms.components.is_strongly_connected(Gd)
```

```python
Hd = Gd.copy()
while Hd.number_of_edges() > 600:
    es = [e for e in Hd.edges]
    er = random.choice(es)
    Hd.remove_edges_from([er])
    if not nx.algorithms.components.is_strongly_connected(Hd):
        Hd.add_edges_from([er])
nx.info(Hd), nx.info(Gd), nx.algorithms.components.is_strongly_connected(Hd)
```

```python
while Hd.number_of_edges() > 300:
    es = [e for e in Hd.edges]
    er = random.choice(es)
    Hd.remove_edges_from([er])
    if not nx.algorithms.components.is_connected(Hd.to_undirected(as_view=True)):
        Hd.add_edges_from([er])
(nx.info(Hd), nx.info(Gd), 
 nx.algorithms.components.is_strongly_connected(Hd), 
 nx.algorithms.components.is_connected(Hd.to_undirected(as_view=True))
)
```

```python
nx.degree_histogram(Hd)
```

```python
nx.degree_histogram(Gd)
```

## Write the graph to a `json` file.

```python
jd = json_graph.adjacency_data(Hd)
with open('sample-graph.json', 'w') as f:
    f.write(json.dumps(jd))
```

Red the graph back in like this:

```python
with open('sample-graph.json') as f:
    jd2 = json.load(f)
H = json_graph.adjacency_graph(jd2)
```

```python
nx.info(H)
```

```python
nx.degree_histogram(H)
```

```python
nx.is_isomorphic(Hd, H)
```

```python

```

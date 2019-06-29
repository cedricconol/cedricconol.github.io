---
layout: post
title:  "Jupyter Generated Blog"
author: john
categories: [ Jupyter, tutorial ]
tags: [red, yellow]
image: assets/images/jupyter-blog-image.png
description: "Generate a blog from Jupyter Notebook"
featured: true
hidden: true
rating: 4.5
---


This blog post was created using Jupyter Notebook.

Sample python code block.


```python
apple = 3
orange = 5

print(apple+orange)
```

    8
    

Importing packages.


```python
import pandas as pd
import matplotlib.pyplot as plt
%matplotlib inline
```

Displaying pandas dataframe.


```python
df = pd.DataFrame({'col1': [1, 2, 3], 'col2': ['a', 'b', 'c']})
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>col1</th>
      <th>col2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>a</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>b</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>c</td>
    </tr>
  </tbody>
</table>
</div>



Displaying plots


```python
df['col1'].plot()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x290c5392f28>




![png]({{ site.baseurl }}/assets/images/output_8_1.png)


Interactive plot.


```python
import plotly.graph_objs as go
import plotly.offline as py
import numpy as np
init_notebook_mode(connected=False)
```

```python
trace0 = go.Bar(
    x=['Feature A', 'Feature B', 'Feature C',
       'Feature D', 'Feature E'],
    y=[20, 14, 23, 25, 22],
    marker=dict(
        color=['rgba(204,204,204,1)', 'rgba(222,45,38,0.8)',
               'rgba(204,204,204,1)', 'rgba(204,204,204,1)',
               'rgba(204,204,204,1)']),
)

data = [trace0]
layout = go.Layout(
    title='Least Used Feature',
)

fig = go.Figure(data=data, layout=layout)
py.iplot(fig, filename='color-bar')
```

<script src="https://cdn.plot.ly/plotly-latest.min.js"></script>
<div id="7db3ddfb-934a-4026-a3ad-db1afc59ab02" style="height: 100%; width: 100%;" class="plotly-graph-div"></div><script type="text/javascript">window.PLOTLYENV=window.PLOTLYENV || {};window.PLOTLYENV.BASE_URL="https://plot.ly";Plotly.newPlot("7db3ddfb-934a-4026-a3ad-db1afc59ab02", [{"type": "bar", "x": ["Feature A", "Feature B", "Feature C", "Feature D", "Feature E"], "y": [20, 14, 23, 25, 22], "marker": {"color": ["rgba(204,204,204,1)", "rgba(222,45,38,0.8)", "rgba(204,204,204,1)", "rgba(204,204,204,1)", "rgba(204,204,204,1)"]}}], {"title": "Least Used Feature"}, {"showLink": true, "linkText": "Export to plot.ly"})</script>

Download this notebook as html.


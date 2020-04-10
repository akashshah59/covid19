# CORONA VIRUS DATA ANALYSIS. 

This notebook aims uses publicly available data made available by the datameet group. They scrape data from trustable government sites as mentioned on their github. In addition to this they have also done a great job at scraping data from non governmental sites like twitter , to collect data on the number of non coronavirus deaths that are taking place in India.

## Data source

https://github.com/datameet/covid19/tree/master/data

## API used to fetch data

https://api.github.com <br>
Accept: application/vnd.github.v3+json <br>
GET /repos/:owner/:repo/contents/:path <br>


```python
import requests
```


```python
help(requests.get)
```

    Help on function get in module requests.api:
    
    get(url, params=None, **kwargs)
        Sends a GET request.
        
        :param url: URL for the new :class:`Request` object.
        :param params: (optional) Dictionary, list of tuples or bytes to send
            in the query string for the :class:`Request`.
        :param \*\*kwargs: Optional arguments that ``request`` takes.
        :return: :class:`Response <Response>` object
        :rtype: requests.Response
    



```python
response = requests.get('https://api.github.com/repos/datameet/covid19/contents/data',params = {'Accept':'application/vnd.github.v3+json'})
```


```python
downloads = {file['name']:requests.get(file['download_url']) for file in eval(response.text)}
downloads
```




    {'all_totals.json': <Response [200]>,
     'icmr_testing_status.json': <Response [200]>,
     'mohfw.json': <Response [200]>,
     'non_virus_deaths.json': <Response [200]>,
     'total_confirmed_cases.json': <Response [200]>}




```python
import pandas as pd
from io import StringIO
import numpy as np
import json
```


```python
mohfw = pd.read_json(StringIO(json.dumps([obj['value'] for obj in json.loads(downloads['mohfw.json'].text)['rows']])),orient = 'records')

icmr_testing = pd.read_json(StringIO(json.dumps([obj['value'] for obj in json.loads(downloads['icmr_testing_status.json'].text)['rows']])),orient = 'records')

non_virus_deaths = pd.read_json(StringIO(json.dumps([obj['value'] for obj in json.loads(downloads['non_virus_deaths.json'].text)['rows']])),orient = 'records')

total_confirmed_cases = pd.read_json(StringIO(json.dumps([{'date':obj['key'][0],'cases':obj['value']} for obj in json.loads(downloads['total_confirmed_cases.json'].text)['rows']])),orient = 'records')

all_totals = pd.read_json(StringIO(json.dumps([{'day':obj['key'][0],'type':obj['key'][1],'value':obj['value']} for obj in json.loads(downloads['all_totals.json'].text)['rows']])),orient = 'records')
```


```python
all_set_totals = all_totals.set_index('day').pivot(columns = 'type').reset_index()
```


```python
!pip install plotly
```

    Collecting plotly
    [?25l  Downloading https://files.pythonhosted.org/packages/15/90/918bccb0ca60dc6d126d921e2c67126d75949f5da777e6b18c51fb12603d/plotly-4.6.0-py2.py3-none-any.whl (7.1MB)
    [K     |████████████████████████████████| 7.2MB 355kB/s eta 0:00:01
    [?25hRequirement already satisfied: six in /home/akash/anaconda3/lib/python3.7/site-packages (from plotly) (1.12.0)
    Collecting retrying>=1.3.3 (from plotly)
      Downloading https://files.pythonhosted.org/packages/44/ef/beae4b4ef80902f22e3af073397f079c96969c69b2c7d52a57ea9ae61c9d/retrying-1.3.3.tar.gz
    Building wheels for collected packages: retrying
      Building wheel for retrying (setup.py) ... [?25ldone
    [?25h  Created wheel for retrying: filename=retrying-1.3.3-cp37-none-any.whl size=11429 sha256=b27e970bdfc908c71f1ac6a47a5ff2ed4e66ef8372e685f745192a14416b8d97
      Stored in directory: /home/akash/.cache/pip/wheels/d7/a9/33/acc7b709e2a35caa7d4cae442f6fe6fbf2c43f80823d46460c
    Successfully built retrying
    Installing collected packages: retrying, plotly
    Successfully installed plotly-4.6.0 retrying-1.3.3


### What does a graph of the number of cases per day look like in India?


```python
import plotly.graph_objs as go
```


```python
fig = go.Figure(data = [go.Scatter(name = 'no. of cases',
                              x = total_confirmed_cases['date'],
                              y = total_confirmed_cases['cases'],
                             marker = {'opacity':1,'showscale':False})],
                
               layout = go.Layout(title = 'Total number of cases in India over time.',
                                  xaxis = {'title':'Date'},
                                  yaxis = {'title':'No. of cases'},
                                  plot_bgcolor = 'white',
                                 showlegend = False
                                )
               )
fig.show()
```


<div>


            <div id="8d4b5169-9ffb-47a2-a0ce-1112c4e3ef86" class="plotly-graph-div" style="height:525px; width:100%;"></div>
            <script type="text/javascript">
                require(["plotly"], function(Plotly) {
                    window.PLOTLYENV=window.PLOTLYENV || {};

                if (document.getElementById("8d4b5169-9ffb-47a2-a0ce-1112c4e3ef86")) {
                    Plotly.newPlot(
                        '8d4b5169-9ffb-47a2-a0ce-1112c4e3ef86',
                        [{"marker": {"opacity": 1, "showscale": false}, "name": "no. of cases", "type": "scatter", "x": ["2020-01-30T13:33:00+05:30", "2020-02-02T10:39:00+05:30", "2020-02-03T12:13:00+05:30", "2020-03-02T14:28:00+05:30", "2020-03-03T19:36:00+05:30", "2020-03-10T12:00:00+05:30", "2020-03-11T17:30:00+05:30", "2020-03-12T11:00:00+05:30", "2020-03-12T18:00:00+05:30", "2020-03-13T10:00:00+05:30", "2020-03-13T22:15:00+05:30", "2020-03-14T09:00:00+05:30", "2020-03-14T16:55:00+05:30", "2020-03-15T08:55:00+05:30", "2020-03-15T12:00:00+05:30", "2020-03-15T17:00:00+05:30", "2020-03-15T23:30:00+05:30", "2020-03-16T16:00:00+05:30", "2020-03-17T09:15:00+05:30", "2020-03-17T10:55:00+05:30", "2020-03-17T11:52:00+05:30", "2020-03-17T17:15:00+05:30", "2020-03-18T09:00:00+05:30", "2020-03-18T17:15:00+05:30", "2020-03-19T09:00:00+05:30", "2020-03-19T16:30:00+05:30", "2020-03-19T17:00:00+05:30", "2020-03-20T09:00:00+05:30", "2020-03-20T17:00:00+05:30", "2020-03-21T09:00:00+05:30", "2020-03-21T16:45:00+05:30", "2020-03-22T09:45:00+05:30", "2020-03-22T11:45:00+05:30", "2020-03-22T18:30:00+05:30", "2020-03-23T09:00:00+05:30", "2020-03-23T10:30:00+05:30", "2020-03-23T18:00:00+05:30", "2020-03-23T20:15:00+05:30", "2020-03-24T08:45:00+05:30", "2020-03-24T17:45:00+05:30", "2020-03-24T20:15:00+05:30", "2020-03-25T09:15:00+05:30", "2020-03-25T18:45:00+05:30", "2020-03-26T10:15:00+05:30", "2020-03-26T20:00:00+05:30", "2020-03-27T09:15:00+05:30", "2020-03-28T09:30:00+05:30", "2020-03-28T17:45:00+05:30", "2020-03-29T10:00:00+05:30", "2020-03-29T19:30:00+05:30", "2020-03-30T10:30:00+05:30", "2020-03-30T21:30:00+05:30", "2020-03-31T20:30:00+05:30", "2020-04-01T09:00:00+05:30", "2020-04-01T19:30:00+05:30", "2020-04-02T09:00:00+05:30", "2020-04-02T18:00:00+05:30", "2020-04-03T09:00:00+05:30", "2020-04-03T18:00:00+05:30", "2020-04-04T09:00:00+05:30", "2020-04-04T18:00:00+05:30", "2020-04-05T09:00:00+05:30", "2020-04-05T18:00:00+05:30", "2020-04-06T09:00:00+05:30", "2020-04-06T18:00:00+05:30", "2020-04-07T09:00:00+05:30", "2020-04-07T18:00:00+05:30", "2020-04-08T08:00:00+05:30", "2020-04-08T17:00:00+05:30"], "y": [1, 2, 3, 5, 6, 47, 60, 73, 74, 75, 79, 83, 84, 93, 107, 107, 110, 114, 125, 125, 126, 137, 147, 151, 166, 167, 173, 195, 223, 258, 283, 324, 324, 360, 390, 390, 433, 434, 492, 519, 519, 562, 606, 649, 694, 724, 873, 909, 979, 1024, 1071, 1205, 1359, 1637, 1834, 1965, 2069, 2283, 2470, 2902, 3072, 3374, 3577, 4067, 4281, 4421, 4789, 5194, 5274]}],
                        {"plot_bgcolor": "white", "showlegend": false, "template": {"data": {"bar": [{"error_x": {"color": "#2a3f5f"}, "error_y": {"color": "#2a3f5f"}, "marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "baxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "pie": [{"automargin": true, "type": "pie"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#EBF0F8"}, "line": {"color": "white"}}, "header": {"fill": {"color": "#C8D4E3"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#2a3f5f", "arrowhead": 0, "arrowwidth": 1}, "coloraxis": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#2a3f5f"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "#E5ECF6", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "light"}, "paper_bgcolor": "white", "plot_bgcolor": "#E5ECF6", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "radialaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"line": {"color": "#2a3f5f"}}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "caxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "title": {"x": 0.05}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "title": {"standoff": 15}, "zerolinecolor": "white", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "title": {"standoff": 15}, "zerolinecolor": "white", "zerolinewidth": 2}}}, "title": {"text": "Total number of cases in India over time."}, "xaxis": {"title": {"text": "Date"}}, "yaxis": {"title": {"text": "No. of cases"}}},
                        {"responsive": true}
                    ).then(function(){

var gd = document.getElementById('8d4b5169-9ffb-47a2-a0ce-1112c4e3ef86');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })
                };
                });
            </script>
        </div>


### What does a graph of the growth of cases per day look like in India?


```python
total_confirmed_cases['growth'] = total_confirmed_cases['cases']
```


```python
for i in range(total_confirmed_cases.shape[0]-1,0,-1):
    total_confirmed_cases['growth'].loc[i] = total_confirmed_cases['growth'].loc[i] - total_confirmed_cases['growth'].loc[i-1] 
    
```


```python
total_confirmed_cases.head(10)
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
      <th>date</th>
      <th>cases</th>
      <th>growth</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2020-01-30 13:33:00+05:30</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2020-02-02 10:39:00+05:30</td>
      <td>2</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2020-02-03 12:13:00+05:30</td>
      <td>3</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2020-03-02 14:28:00+05:30</td>
      <td>5</td>
      <td>2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2020-03-03 19:36:00+05:30</td>
      <td>6</td>
      <td>1</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2020-03-10 12:00:00+05:30</td>
      <td>47</td>
      <td>41</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2020-03-11 17:30:00+05:30</td>
      <td>60</td>
      <td>13</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2020-03-12 11:00:00+05:30</td>
      <td>73</td>
      <td>13</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2020-03-12 18:00:00+05:30</td>
      <td>74</td>
      <td>1</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2020-03-13 10:00:00+05:30</td>
      <td>75</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
fig = go.Figure(data = [go.Scatter(name = 'no. of cases per day',
                              x = total_confirmed_cases['date'],
                              y = total_confirmed_cases['growth'],
                             marker = {'opacity':1,'showscale':False}),
                        go.Scatter(name = 'Moving average',
                              x = total_confirmed_cases['date'],
                              y = total_confirmed_cases['growth'].rolling(7).mean(),
                             marker = {'opacity':1,'showscale':False})
                       ],
                
               layout = go.Layout(title = 'Growth in number of cases in India over per day.',
                                  xaxis = {'title':'Date'},
                                  yaxis = {'title':'Growth in no. of cases'},
                                  plot_bgcolor = 'white',
                                 showlegend = False
                                )
               )
fig.show()
```


<div>


            <div id="e3ef484c-cd4d-47d0-aae4-05e241b46db1" class="plotly-graph-div" style="height:525px; width:100%;"></div>
            <script type="text/javascript">
                require(["plotly"], function(Plotly) {
                    window.PLOTLYENV=window.PLOTLYENV || {};

                if (document.getElementById("e3ef484c-cd4d-47d0-aae4-05e241b46db1")) {
                    Plotly.newPlot(
                        'e3ef484c-cd4d-47d0-aae4-05e241b46db1',
                        [{"marker": {"opacity": 1, "showscale": false}, "name": "no. of cases per day", "type": "scatter", "x": ["2020-01-30T13:33:00+05:30", "2020-02-02T10:39:00+05:30", "2020-02-03T12:13:00+05:30", "2020-03-02T14:28:00+05:30", "2020-03-03T19:36:00+05:30", "2020-03-10T12:00:00+05:30", "2020-03-11T17:30:00+05:30", "2020-03-12T11:00:00+05:30", "2020-03-12T18:00:00+05:30", "2020-03-13T10:00:00+05:30", "2020-03-13T22:15:00+05:30", "2020-03-14T09:00:00+05:30", "2020-03-14T16:55:00+05:30", "2020-03-15T08:55:00+05:30", "2020-03-15T12:00:00+05:30", "2020-03-15T17:00:00+05:30", "2020-03-15T23:30:00+05:30", "2020-03-16T16:00:00+05:30", "2020-03-17T09:15:00+05:30", "2020-03-17T10:55:00+05:30", "2020-03-17T11:52:00+05:30", "2020-03-17T17:15:00+05:30", "2020-03-18T09:00:00+05:30", "2020-03-18T17:15:00+05:30", "2020-03-19T09:00:00+05:30", "2020-03-19T16:30:00+05:30", "2020-03-19T17:00:00+05:30", "2020-03-20T09:00:00+05:30", "2020-03-20T17:00:00+05:30", "2020-03-21T09:00:00+05:30", "2020-03-21T16:45:00+05:30", "2020-03-22T09:45:00+05:30", "2020-03-22T11:45:00+05:30", "2020-03-22T18:30:00+05:30", "2020-03-23T09:00:00+05:30", "2020-03-23T10:30:00+05:30", "2020-03-23T18:00:00+05:30", "2020-03-23T20:15:00+05:30", "2020-03-24T08:45:00+05:30", "2020-03-24T17:45:00+05:30", "2020-03-24T20:15:00+05:30", "2020-03-25T09:15:00+05:30", "2020-03-25T18:45:00+05:30", "2020-03-26T10:15:00+05:30", "2020-03-26T20:00:00+05:30", "2020-03-27T09:15:00+05:30", "2020-03-28T09:30:00+05:30", "2020-03-28T17:45:00+05:30", "2020-03-29T10:00:00+05:30", "2020-03-29T19:30:00+05:30", "2020-03-30T10:30:00+05:30", "2020-03-30T21:30:00+05:30", "2020-03-31T20:30:00+05:30", "2020-04-01T09:00:00+05:30", "2020-04-01T19:30:00+05:30", "2020-04-02T09:00:00+05:30", "2020-04-02T18:00:00+05:30", "2020-04-03T09:00:00+05:30", "2020-04-03T18:00:00+05:30", "2020-04-04T09:00:00+05:30", "2020-04-04T18:00:00+05:30", "2020-04-05T09:00:00+05:30", "2020-04-05T18:00:00+05:30", "2020-04-06T09:00:00+05:30", "2020-04-06T18:00:00+05:30", "2020-04-07T09:00:00+05:30", "2020-04-07T18:00:00+05:30", "2020-04-08T08:00:00+05:30", "2020-04-08T17:00:00+05:30"], "y": [1, 1, 1, 2, 1, 41, 13, 13, 1, 1, 4, 4, 1, 9, 14, 0, 3, 4, 11, 0, 1, 11, 10, 4, 15, 1, 6, 22, 28, 35, 25, 41, 0, 36, 30, 0, 43, 1, 58, 27, 0, 43, 44, 43, 45, 30, 149, 36, 70, 45, 47, 134, 154, 278, 197, 131, 104, 214, 187, 432, 170, 302, 203, 490, 214, 140, 368, 405, 80]}, {"marker": {"opacity": 1, "showscale": false}, "name": "Moving average", "type": "scatter", "x": ["2020-01-30T13:33:00+05:30", "2020-02-02T10:39:00+05:30", "2020-02-03T12:13:00+05:30", "2020-03-02T14:28:00+05:30", "2020-03-03T19:36:00+05:30", "2020-03-10T12:00:00+05:30", "2020-03-11T17:30:00+05:30", "2020-03-12T11:00:00+05:30", "2020-03-12T18:00:00+05:30", "2020-03-13T10:00:00+05:30", "2020-03-13T22:15:00+05:30", "2020-03-14T09:00:00+05:30", "2020-03-14T16:55:00+05:30", "2020-03-15T08:55:00+05:30", "2020-03-15T12:00:00+05:30", "2020-03-15T17:00:00+05:30", "2020-03-15T23:30:00+05:30", "2020-03-16T16:00:00+05:30", "2020-03-17T09:15:00+05:30", "2020-03-17T10:55:00+05:30", "2020-03-17T11:52:00+05:30", "2020-03-17T17:15:00+05:30", "2020-03-18T09:00:00+05:30", "2020-03-18T17:15:00+05:30", "2020-03-19T09:00:00+05:30", "2020-03-19T16:30:00+05:30", "2020-03-19T17:00:00+05:30", "2020-03-20T09:00:00+05:30", "2020-03-20T17:00:00+05:30", "2020-03-21T09:00:00+05:30", "2020-03-21T16:45:00+05:30", "2020-03-22T09:45:00+05:30", "2020-03-22T11:45:00+05:30", "2020-03-22T18:30:00+05:30", "2020-03-23T09:00:00+05:30", "2020-03-23T10:30:00+05:30", "2020-03-23T18:00:00+05:30", "2020-03-23T20:15:00+05:30", "2020-03-24T08:45:00+05:30", "2020-03-24T17:45:00+05:30", "2020-03-24T20:15:00+05:30", "2020-03-25T09:15:00+05:30", "2020-03-25T18:45:00+05:30", "2020-03-26T10:15:00+05:30", "2020-03-26T20:00:00+05:30", "2020-03-27T09:15:00+05:30", "2020-03-28T09:30:00+05:30", "2020-03-28T17:45:00+05:30", "2020-03-29T10:00:00+05:30", "2020-03-29T19:30:00+05:30", "2020-03-30T10:30:00+05:30", "2020-03-30T21:30:00+05:30", "2020-03-31T20:30:00+05:30", "2020-04-01T09:00:00+05:30", "2020-04-01T19:30:00+05:30", "2020-04-02T09:00:00+05:30", "2020-04-02T18:00:00+05:30", "2020-04-03T09:00:00+05:30", "2020-04-03T18:00:00+05:30", "2020-04-04T09:00:00+05:30", "2020-04-04T18:00:00+05:30", "2020-04-05T09:00:00+05:30", "2020-04-05T18:00:00+05:30", "2020-04-06T09:00:00+05:30", "2020-04-06T18:00:00+05:30", "2020-04-07T09:00:00+05:30", "2020-04-07T18:00:00+05:30", "2020-04-08T08:00:00+05:30", "2020-04-08T17:00:00+05:30"], "y": [null, null, null, null, null, null, 8.571428571428571, 10.285714285714286, 10.285714285714286, 10.285714285714286, 10.571428571428571, 11.0, 5.285714285714286, 4.714285714285714, 4.857142857142857, 4.714285714285714, 5.0, 5.0, 6.0, 5.857142857142857, 4.714285714285714, 4.285714285714286, 5.714285714285714, 5.857142857142857, 7.428571428571429, 6.0, 6.857142857142857, 9.857142857142858, 12.285714285714286, 15.857142857142858, 18.857142857142858, 22.571428571428573, 22.428571428571427, 26.714285714285715, 27.857142857142858, 23.857142857142858, 25.0, 21.571428571428573, 24.0, 27.857142857142858, 22.714285714285715, 24.571428571428573, 30.857142857142858, 30.857142857142858, 37.142857142857146, 33.142857142857146, 50.57142857142857, 55.714285714285715, 59.57142857142857, 59.714285714285715, 60.285714285714285, 73.0, 90.71428571428571, 109.14285714285714, 132.14285714285714, 140.85714285714286, 149.28571428571428, 173.14285714285714, 180.71428571428572, 220.42857142857142, 205.0, 220.0, 230.28571428571428, 285.42857142857144, 285.42857142857144, 278.7142857142857, 269.57142857142856, 303.14285714285717, 271.42857142857144]}],
                        {"plot_bgcolor": "white", "showlegend": false, "template": {"data": {"bar": [{"error_x": {"color": "#2a3f5f"}, "error_y": {"color": "#2a3f5f"}, "marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "baxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "pie": [{"automargin": true, "type": "pie"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#EBF0F8"}, "line": {"color": "white"}}, "header": {"fill": {"color": "#C8D4E3"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#2a3f5f", "arrowhead": 0, "arrowwidth": 1}, "coloraxis": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#2a3f5f"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "#E5ECF6", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "light"}, "paper_bgcolor": "white", "plot_bgcolor": "#E5ECF6", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "radialaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"line": {"color": "#2a3f5f"}}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "caxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "title": {"x": 0.05}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "title": {"standoff": 15}, "zerolinecolor": "white", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "title": {"standoff": 15}, "zerolinecolor": "white", "zerolinewidth": 2}}}, "title": {"text": "Growth in number of cases in India over per day."}, "xaxis": {"title": {"text": "Date"}}, "yaxis": {"title": {"text": "Growth in no. of cases"}}},
                        {"responsive": true}
                    ).then(function(){

var gd = document.getElementById('e3ef484c-cd4d-47d0-aae4-05e241b46db1');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })
                };
                });
            </script>
        </div>


Number of cases are not increasing every day. It fluctuates quite a lot , this however could depend on a lot of things , like number of tests done that day . The general trend is however upwards.

### Total number of non virus deaths


```python
non_virus_deaths.deaths.sum()
```




    137



### Total number of virus deaths


```python
all_set_totals.value.death.iloc[-1:]
```




    68    149
    Name: death, dtype: int64




```python
non_virus_modified = pd.get_dummies(non_virus_deaths.explode('reason'),columns = ['reason'],prefix = None)
```


```python
non_virus_modified.rename(columns=lambda x: x.replace('reason_',''), inplace=True)
```


```python
non_virus_modified.columns
```




    Index(['_id', '_rev', 'type', 'location', 'district', 'state', 'incident_date',
           'deaths', 'source_date', 'source_link', 'source',
           'Access to care denied', 'Asked to leave after lockdown',
           'Asphyxiation', 'Assault', 'Buried under snow', 'Death',
           'Death by waiting in the sun', 'Dehydration', 'Delay in treatment',
           'Died under mysterious circumstances', 'Drank aftershave',
           'Exhausation', 'Exhaustion', 'Farmer suicide', 'Fear of infection',
           'Fear of lockdown', 'Fear of police after escaping quaratine',
           'Forest fire', 'Front-line medical staff work stress', 'Got sick',
           'Had cancer', 'Hate crime', 'Health deterioration', 'Home qurantine',
           'Icu closed', 'Lack of transportation', 'Lack of treatment',
           'Lack of work & food', 'Lathicharge',
           'Left untreated by doctor due to lockdown', 'Lockdown',
           'Lockdown caused loss of livelihood', 'Lonliness',
           'Lost balance of the bike and hit divider while going home',
           'Medical emergency', 'Medical negligance', 'Migration',
           'No staff around', 'Police beating', 'Police brutality', 'Quarantine',
           'Road accident', 'Road blocked', 'Roadblock', 'Starvation', 'Stigma',
           'Stray dog snatched a newborn', 'Stuck under a pit during migration',
           'Sucide', 'Suicide', 'Suspected patient',
           'Travelled by moped with family',
           'Unclear - financial issues (not connected to covid directly) or isolation',
           'Walking', 'Withdrawal', 'Withdrawal symptoms', '\withdrawal'],
          dtype='object')




```python
reasons = ['Access to care denied', 'Asked to leave after lockdown',
       'Asphyxiation', 'Assault', 'Buried under snow', 'Death',
       'Death by waiting in the sun', 'Dehydration', 'Delay in treatment',
       'Died under mysterious circumstances', 'Drank aftershave',
       'Exhausation', 'Exhaustion', 'Farmer suicide', 'Fear of infection',
       'Fear of lockdown', 'Fear of police after escaping quaratine',
       'Forest fire', 'Front-line medical staff work stress', 'Got sick',
       'Had cancer', 'Hate crime', 'Health deterioration', 'Home qurantine',
       'Icu closed', 'Lack of transportation', 'Lack of treatment',
       'Lack of work & food', 'Lathicharge',
       'Left untreated by doctor due to lockdown', 'Lockdown',
       'Lockdown caused loss of livelihood', 'Lonliness',
       'Lost balance of the bike and hit divider while going home',
       'Medical emergency', 'Medical negligance', 'Migration',
       'No staff around', 'Police beating', 'Police brutality', 'Quarantine',
       'Road accident', 'Road blocked', 'Roadblock', 'Starvation', 'Stigma',
       'Stray dog snatched a newborn', 'Stuck under a pit during migration',
       'Sucide', 'Suicide', 'Suspected patient',
       'Travelled by moped with family',
       'Unclear - financial issues (not connected to covid directly) or isolation',
       'Walking', 'Withdrawal', 'Withdrawal symptoms', '\withdrawal']

for column in reasons:
    non_virus_modified[column] = non_virus_modified[column].mul(non_virus_modified['deaths'])
```


```python
non_virus_modified.columns[11:]
```




    Index(['Access to care denied', 'Asked to leave after lockdown',
           'Asphyxiation', 'Assault', 'Buried under snow', 'Death',
           'Death by waiting in the sun', 'Dehydration', 'Delay in treatment',
           'Died under mysterious circumstances', 'Drank aftershave',
           'Exhausation', 'Exhaustion', 'Farmer suicide', 'Fear of infection',
           'Fear of lockdown', 'Fear of police after escaping quaratine',
           'Forest fire', 'Front-line medical staff work stress', 'Got sick',
           'Had cancer', 'Hate crime', 'Health deterioration', 'Home qurantine',
           'Icu closed', 'Lack of transportation', 'Lack of treatment',
           'Lack of work & food', 'Lathicharge',
           'Left untreated by doctor due to lockdown', 'Lockdown',
           'Lockdown caused loss of livelihood', 'Lonliness',
           'Lost balance of the bike and hit divider while going home',
           'Medical emergency', 'Medical negligance', 'Migration',
           'No staff around', 'Police beating', 'Police brutality', 'Quarantine',
           'Road accident', 'Road blocked', 'Roadblock', 'Starvation', 'Stigma',
           'Stray dog snatched a newborn', 'Stuck under a pit during migration',
           'Sucide', 'Suicide', 'Suspected patient',
           'Travelled by moped with family',
           'Unclear - financial issues (not connected to covid directly) or isolation',
           'Walking', 'Withdrawal', 'Withdrawal symptoms', '\withdrawal'],
          dtype='object')




```python
non_virus_modified['Suicide'] = non_virus_modified['Suicide'].add(non_virus_modified['Sucide'])
non_virus_modified['Withdrawal'] = non_virus_modified['Withdrawal'].add(non_virus_modified['Withdrawal symptoms'])
non_virus_modified['Withdrawal'] = non_virus_modified['Withdrawal'].add(non_virus_modified['\withdrawal'])
```


```python
non_virus_modified.drop(columns = ['Suicide','Withdrawal symptoms','\withdrawal'],inplace = True)
```


```python
non_virus_modified[non_virus_modified.columns[11:]].head(5)
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
      <th>Access to care denied</th>
      <th>Asked to leave after lockdown</th>
      <th>Asphyxiation</th>
      <th>Assault</th>
      <th>Buried under snow</th>
      <th>Death</th>
      <th>Death by waiting in the sun</th>
      <th>Dehydration</th>
      <th>Delay in treatment</th>
      <th>Died under mysterious circumstances</th>
      <th>...</th>
      <th>Starvation</th>
      <th>Stigma</th>
      <th>Stray dog snatched a newborn</th>
      <th>Stuck under a pit during migration</th>
      <th>Sucide</th>
      <th>Suspected patient</th>
      <th>Travelled by moped with family</th>
      <th>Unclear - financial issues (not connected to covid directly) or isolation</th>
      <th>Walking</th>
      <th>Withdrawal</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 54 columns</p>
</div>




```python
causes_and_deaths = non_virus_modified[non_virus_modified.columns[11:]].sum()
```


```python
fig = go.Figure(data=[go.Pie(labels = causes_and_deaths.index, values=causes_and_deaths)])
fig.show()
```


<div>


            <div id="c548c267-395a-43f3-9916-d6f8fc2a635d" class="plotly-graph-div" style="height:525px; width:100%;"></div>
            <script type="text/javascript">
                require(["plotly"], function(Plotly) {
                    window.PLOTLYENV=window.PLOTLYENV || {};

                if (document.getElementById("c548c267-395a-43f3-9916-d6f8fc2a635d")) {
                    Plotly.newPlot(
                        'c548c267-395a-43f3-9916-d6f8fc2a635d',
                        [{"labels": ["Access to care denied", "Asked to leave after lockdown", "Asphyxiation", "Assault", "Buried under snow", "Death", "Death by waiting in the sun", "Dehydration", "Delay in treatment", "Died under mysterious circumstances", "Drank aftershave", "Exhausation", "Exhaustion", "Farmer suicide", "Fear of infection", "Fear of lockdown", "Fear of police after escaping quaratine", "Forest fire", "Front-line medical staff work stress", "Got sick", "Had cancer", "Hate crime", "Health deterioration", "Home qurantine", "Icu closed", "Lack of transportation", "Lack of treatment", "Lack of work & food", "Lathicharge", "Left untreated by doctor due to lockdown", "Lockdown", "Lockdown caused loss of livelihood", "Lonliness", "Lost balance of the bike and hit divider while going home", "Medical emergency", "Medical negligance", "Migration", "No staff around", "Police beating", "Police brutality", "Quarantine", "Road accident", "Road blocked", "Roadblock", "Starvation", "Stigma", "Stray dog snatched a newborn", "Stuck under a pit during migration", "Sucide", "Suspected patient", "Travelled by moped with family", "Unclear - financial issues (not connected to covid directly) or isolation", "Walking", "Withdrawal"], "type": "pie", "values": [1, 1, 3, 1, 3, 2, 1, 1, 1, 1, 2, 1, 4, 1, 15, 1, 1, 4, 2, 1, 2, 1, 1, 3, 1, 1, 1, 1, 2, 1, 34, 1, 2, 1, 5, 1, 37, 1, 2, 1, 2, 32, 4, 7, 7, 1, 1, 3, 2, 1, 2, 1, 2, 13]}],
                        {"template": {"data": {"bar": [{"error_x": {"color": "#2a3f5f"}, "error_y": {"color": "#2a3f5f"}, "marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "baxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "pie": [{"automargin": true, "type": "pie"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#EBF0F8"}, "line": {"color": "white"}}, "header": {"fill": {"color": "#C8D4E3"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#2a3f5f", "arrowhead": 0, "arrowwidth": 1}, "coloraxis": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#2a3f5f"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "#E5ECF6", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "light"}, "paper_bgcolor": "white", "plot_bgcolor": "#E5ECF6", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "radialaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"line": {"color": "#2a3f5f"}}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "caxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "title": {"x": 0.05}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "title": {"standoff": 15}, "zerolinecolor": "white", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "title": {"standoff": 15}, "zerolinecolor": "white", "zerolinewidth": 2}}}},
                        {"responsive": true}
                    ).then(function(){

var gd = document.getElementById('c548c267-395a-43f3-9916-d6f8fc2a635d');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })
                };
                });
            </script>
        </div>


This graph tells us that 15% (37) of the non virus deaths reported in India are due to lockdown or migration. This can prove to be a seperate problem to India where relaxing lockdown policy would become necessary if this trend continues within the next 2 weeks.

### What does the number of positive to testing ratio look like ? 


```python
us_response = requests.get('https://covidtracking.com/api/us/daily.csv')
```


```python
us_data = pd.read_csv(StringIO(us_response.text))
```


```python
us_tests = us_data.positive / (us_data.total + us_data.pending + us_data.negative)
india_tests = icmr_testing.confirmed_positive / icmr_testing.samples
india_tests.index = india_tests.index + 9
```


```python
fig = go.Figure(data = [go.Scatter(name = 'US positive to test ratio',
                              x = list(us_tests.index),
                              y = list(us_tests)[::-1],
                             marker = {'opacity':1,'showscale':False}),
                        go.Scatter(name = 'India positive to test ratio',
                              x = list(india_tests.index),
                              y = india_tests,
                             marker = {'opacity':1,'showscale':False})
                       ],
                
               layout = go.Layout(title = 'US vs India positive to test ratio',
                                  xaxis = {'title':'Date'},
                                  yaxis = {'title':'%Tested positive'},
                                 showlegend = False
                                )
               )
fig.show()
```


<div>


            <div id="995e5732-35ea-444e-ba8d-dc2364f22cd1" class="plotly-graph-div" style="height:525px; width:100%;"></div>
            <script type="text/javascript">
                require(["plotly"], function(Plotly) {
                    window.PLOTLYENV=window.PLOTLYENV || {};

                if (document.getElementById("995e5732-35ea-444e-ba8d-dc2364f22cd1")) {
                    Plotly.newPlot(
                        '995e5732-35ea-444e-ba8d-dc2364f22cd1',
                        [{"marker": {"opacity": 1, "showscale": false}, "name": "US positive to test ratio", "type": "scatter", "x": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35], "y": [0.06483516483516484, 0.07108239095315025, 0.05209063302966597, 0.06604687197365873, 0.07213284898806435, 0.07351460221550855, 0.08338692390139335, 0.07360335195530726, 0.0701595262231233, 0.06119460010188487, 0.06261820784133312, 0.06014139767622586, 0.05048424173146252, 0.05485887406043872, 0.05322518453233447, 0.05977343323625272, 0.06551481420224857, 0.06783720474570919, 0.07509970811964503, 0.0772105155183025, 0.07796499273683326, 0.07251311810491308, 0.07486255648189102, 0.07803003842123646, 0.07964073631004344, 0.08403843283288945, 0.08631034949965483, 0.09088189138343632, 0.09545517524294396, 0.0987767903729614, 0.10280395026488304, 0.10280640936624205, 0.10299748633913655, 0.10353421679918463, 0.10470725792944234, 0.10571610014304865]}, {"marker": {"opacity": 1, "showscale": false}, "name": "India positive to test ratio", "type": "scatter", "x": [9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27], "y": [0.012, 0.011428571428571429, 0.012616401321718233, 0.012839506172839505, 0.014329437952142459, 0.015320695923136847, 0.017260047130756004, 0.02006000352961939, 0.022745931327570388, 0.023101993865030673, 0.023106904231625834, 0.0249566599248772, 0.04287710371003733, 0.038313235612679616, 0.038936835522201375, 0.03969441776308442, 0.040913048640519256, 0.0404859009779415, 0.03997842384634026]}],
                        {"showlegend": false, "template": {"data": {"bar": [{"error_x": {"color": "#2a3f5f"}, "error_y": {"color": "#2a3f5f"}, "marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "baxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "pie": [{"automargin": true, "type": "pie"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#EBF0F8"}, "line": {"color": "white"}}, "header": {"fill": {"color": "#C8D4E3"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#2a3f5f", "arrowhead": 0, "arrowwidth": 1}, "coloraxis": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#2a3f5f"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "#E5ECF6", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "light"}, "paper_bgcolor": "white", "plot_bgcolor": "#E5ECF6", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "radialaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"line": {"color": "#2a3f5f"}}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "caxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "title": {"x": 0.05}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "title": {"standoff": 15}, "zerolinecolor": "white", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "title": {"standoff": 15}, "zerolinecolor": "white", "zerolinewidth": 2}}}, "title": {"text": "US vs India positive to test ratio"}, "xaxis": {"title": {"text": "Date"}}, "yaxis": {"title": {"text": "%Tested positive"}}},
                        {"responsive": true}
                    ).then(function(){

var gd = document.getElementById('995e5732-35ea-444e-ba8d-dc2364f22cd1');
var x = new MutationObserver(function (mutations, observer) {{
        var display = window.getComputedStyle(gd).display;
        if (!display || display === 'none') {{
            console.log([gd, 'removed!']);
            Plotly.purge(gd);
            observer.disconnect();
        }}
}});

// Listen for the removal of the full notebook cells
var notebookContainer = gd.closest('#notebook-container');
if (notebookContainer) {{
    x.observe(notebookContainer, {childList: true});
}}

// Listen for the clearing of the current output cell
var outputEl = gd.closest('.output');
if (outputEl) {{
    x.observe(outputEl, {childList: true});
}}

                        })
                };
                });
            </script>
        </div>



```python

```

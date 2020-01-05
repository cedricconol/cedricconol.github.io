---
layout: post
title:  "Hypothesis Testing on Chess Openings"
categories: [ hypothesis testing, web scraping, sports, chess, chi-square, BeautifulSoup, sqlite3 ]
image: assets/images/chess.jpg
imgsource: "Photo by sk on Unsplash"
description: "Are chess openings statistically related to the outcome of the game"
featured: true
---

In the game of chess, an **opening** refers to the initial moves of a game. The term can refer to the initial moves by either side, white or black, but an opening by black may also be known as a *defense*. There are dozens of different openings, and hundreds of variants. [The Oxford Companion to Chess](https://en.wikipedia.org/wiki/The_Oxford_Companion_to_Chess) lists 1, 327 named openings and variants. Chess players, including professionals, follow different opening moves as part of their strategy.

**The goal of this study is to test if there is a relationship between the type of opening move and the outcome of the game.**

### About the game

For the unfamiliar, chess is a game played on a board of 64 squares by two players. One player is in control of the white pieces, and the other player is in control of the black pieces. The player controlling the white pieces is often simply referred to as "white" and the player controlling the black pieces is often simply referred to as "black". Each player gets eight pawns, two knights, two bishops, two rooks, one queen and one king. The board below shows the starting position for standard chess.

The goal of chess is to trap the enemy king up to the point that it cannot avoid being captured. The white player always goes first followed by black. Each player would take turns moving 1 piece per turn. No one is allowed to "pass" on a turn. The game is won when one king is in 'check' and cannot avoid capture on the next player's move; this is called checkmate. A game also can end when a player acknowledges his or her defeat by resigning. It is also possible for the game to end in a draw, that's when a player is not in check but has no legal moves.

### Data

To meet to the objective of this study, we have to collect chess games records which include the opening used and the outcome. These records can be seen from [chessgames.com](www.chessgames.com), an online chess database and community. Since the data needed for this study is not readily available from the said website, it has to be scraped.

Chessgames.com lists all openings on [this](http://www.chessgames.com/chessecohelp.html) page. For each opening type, all game records that used this opening were scraped and stored in a database. `BeatifulSoup`, a python library for pulling data from HTML and XML files, was used to scrape such data and were stored in a database using `sqlite3`, a module included in the Python standard library intended for working with SQLite databases.

The first 10 rows of the dataset is shown below.

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
      <th>opening</th>
      <th>wplayer</th>
      <th>bplayer</th>
      <th>wres</th>
      <th>bres</th>
      <th>move</th>
      <th>year</th>
      <th>event</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A00</td>
      <td>NN</td>
      <td>Lucena</td>
      <td>0</td>
      <td>1</td>
      <td>26.0</td>
      <td>1497.0</td>
      <td>Salamanca</td>
    </tr>
    <tr>
      <th>1</th>
      <td>A00</td>
      <td>Lucena</td>
      <td>Quintana</td>
      <td>1</td>
      <td>0</td>
      <td>32.0</td>
      <td>1515.0</td>
      <td>Huesca</td>
    </tr>
    <tr>
      <th>2</th>
      <td>A00</td>
      <td>Napoleon Bonaparte</td>
      <td>Madame De Remusat</td>
      <td>1</td>
      <td>0</td>
      <td>14.0</td>
      <td>1804.0</td>
      <td>Chateau de Malmaison</td>
    </tr>
    <tr>
      <th>3</th>
      <td>A00</td>
      <td>Amsterdam</td>
      <td>Rotterdam</td>
      <td>1</td>
      <td>0</td>
      <td>34.0</td>
      <td>1824.0</td>
      <td>City Match</td>
    </tr>
    <tr>
      <th>4</th>
      <td>A00</td>
      <td>Hyderabad</td>
      <td>Madras Chess Club</td>
      <td>0</td>
      <td>1</td>
      <td>44.0</td>
      <td>1828.0</td>
      <td>City Match</td>
    </tr>
    <tr>
      <th>5</th>
      <td>A00</td>
      <td>Kieseritzky</td>
      <td>H H Boncourt</td>
      <td>0</td>
      <td>1</td>
      <td>30.0</td>
      <td>1839.0</td>
      <td>Match?</td>
    </tr>
    <tr>
      <th>6</th>
      <td>A00</td>
      <td>Kieseritzky</td>
      <td>Saint Amant</td>
      <td>½</td>
      <td>½</td>
      <td>50.0</td>
      <td>1843.0</td>
      <td>Paris</td>
    </tr>
    <tr>
      <th>7</th>
      <td>A00</td>
      <td>Enschede</td>
      <td>Zutphen</td>
      <td>½</td>
      <td>½</td>
      <td>58.0</td>
      <td>1850.0</td>
      <td>corr</td>
    </tr>
    <tr>
      <th>8</th>
      <td>A00</td>
      <td>E Williams</td>
      <td>Wyvill</td>
      <td>0</td>
      <td>1</td>
      <td>50.0</td>
      <td>1851.0</td>
      <td>London</td>
    </tr>
    <tr>
      <th>9</th>
      <td>A00</td>
      <td>E Williams</td>
      <td>Harrwitz</td>
      <td>½</td>
      <td>½</td>
      <td>25.0</td>
      <td>1852.0</td>
      <td>Harrwitz - Williams</td>
    </tr>
  </tbody>
</table>
</div>


**Column description**
1. **`opening`**: code of the opening move according to [http://www.chessgames.com/chessecohelp.html](http://www.chessgames.com/chessecohelp.html)
2. **`wplayer`**: name of the player controlling white pieces
3. **`bplayer`**: name of the player controlling black pieces
4. **`wres`**: game outcome for white player
5. **`bres`**: game outcome for black player
6. **`move`**: number of moves taken by white player before the game concludes
7. **`year`**: year of the game
8. **`event`**: name of the event

### Exploratory Data Analysis

Scraped data contains 920,458 games. The earliest game on record dates back 1475, in 21 moves, this match was won by Castellvi over Vinyoles in Valencia. Approximately, a third of the matches resulted in a draw (311831). By barely looking at the winning percentages, it is evident that white enjoys the [first-mover advantage](https://en.wikipedia.org/wiki/First-move_advantage_in_chess), winning 38.2% (351,565) of matches while black only won 27.9% (257,062) of the time.

Figure below shows the top 10 most used openings. 3 most used openings are `A07` (`King's Indian Attack`), `A04` (`Reti Opening`), and `D02` (`Queen's Pawn Game`) which were used in 26,295, 14,386, and 12,883 games respectively.  Hover your mouse pointer on the figure to see the number of games for each of the most used openings. To see the actual names of the openings, please refer to [this](http://www.chessgames.com/chessecohelp.html) link.


<html>
<head><meta charset="utf-8" />
    <script src="https://cdn.plot.ly/plotly-latest.min.js"></script></head>
<body>
    <div>        
            <div id="f0ea7aab-feaa-40ac-a149-47f5a1cda1c8" class="plotly-graph-div" style="height:500px; width:600px;"></div>
            <script type="text/javascript">
                
                    window.PLOTLYENV=window.PLOTLYENV || {};
                    
                if (document.getElementById("f0ea7aab-feaa-40ac-a149-47f5a1cda1c8")) {
                    Plotly.newPlot(
                        'f0ea7aab-feaa-40ac-a149-47f5a1cda1c8',
                        [{"marker": {"color": ["rgba(190, 192, 213, 1)", "rgba(190, 192, 213, 1)", "rgba(190, 192, 213, 1)", "rgba(190, 192, 213, 1)", "rgba(190, 192, 213, 1)", "rgba(190, 192, 213, 1)", "rgba(190, 192, 213, 1)", "rgba(71, 58, 131, 0.8)", "rgba(71, 58, 131, 0.8)", "rgba(71, 58, 131, 0.8)"]}, "orientation": "h", "type": "bar", "x": [8644, 9667, 10001, 10260, 10810, 11124, 11694, 12883, 14386, 26695], "y": ["B01", "A45", "A46", "A15", "B22", "B90", "B06", "D02", "A04", "A07"]}],
                        {"height": 500, "template": {"data": {"bar": [{"error_x": {"color": "#2a3f5f"}, "error_y": {"color": "#2a3f5f"}, "marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "baxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "pie": [{"automargin": true, "type": "pie"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#EBF0F8"}, "line": {"color": "white"}}, "header": {"fill": {"color": "#C8D4E3"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#2a3f5f", "arrowhead": 0, "arrowwidth": 1}, "coloraxis": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#2a3f5f"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "#E5ECF6", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "light"}, "paper_bgcolor": "white", "plot_bgcolor": "#E5ECF6", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "radialaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"line": {"color": "#2a3f5f"}}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "caxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "title": {"x": 0.05}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "title": {"standoff": 15}, "zerolinecolor": "white", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "title": {"standoff": 15}, "zerolinecolor": "white", "zerolinewidth": 2}}}, "title": {"text": "Top 10 Opening Moves by Number of Games Used", "x": 0.5}, "width": 600, "xaxis": {"title": {"text": "Number of Games Used"}}, "yaxis": {"title": {"text": "Openings"}}},
                        {"responsive": true}
                    )
                };
                
            </script>
    </div>
</body>
</html>

But does the popularity of opening always mean winning? To answer this question, we have to look at the winning percentages of the openings. **In this study, it is assumed that openings are used only by the white player thus win percentage is calculated as the number of times white won when using a particular opening divided by the number of games that opening was used in the entire dataset.** 

Please note that some openings only have a few games on record and some of them have 100% win percentage and should therefore be excluded. To filter the less used openings, only the ones with frequency above the 25th percentile were considered or openings which were used on at least 460 (25th percentile) games.

The figure below shows the 10 openings with the highest win percentage. `D06` (`Queen's Gambit Declined`) had the highest win percentage at 62.3% followed by `C37` (`King's Gambit Accepted`) with 56.8% and `C62` (`Ruy Lopez, Old Steinitz Defense`) with 56.6%. The most popular opening, `A07`, ranked 7th with 51.2% win percentage. 

<html>
<head><meta charset="utf-8" />
    <script src="https://cdn.plot.ly/plotly-latest.min.js"></script></head>
<body>
    <div>
        
        
            <div id="0561c8fb-3f8d-4331-8fda-2d1b8cbc04b1" class="plotly-graph-div" style="height:500px; width:600px;"></div>
            <script type="text/javascript">
                
                    window.PLOTLYENV=window.PLOTLYENV || {};
                    
                if (document.getElementById("0561c8fb-3f8d-4331-8fda-2d1b8cbc04b1")) {
                    Plotly.newPlot(
                        '0561c8fb-3f8d-4331-8fda-2d1b8cbc04b1',
                        [{"marker": {"color": ["rgba(190, 192, 213, 1)", "rgba(190, 192, 213, 1)", "rgba(190, 192, 213, 1)", "rgba(190, 192, 213, 1)", "rgba(190, 192, 213, 1)", "rgba(190, 192, 213, 1)", "rgba(190, 192, 213, 1)", "rgba(71, 58, 131, 0.8)", "rgba(71, 58, 131, 0.8)", "rgba(71, 58, 131, 0.8)"]}, "orientation": "h", "type": "bar", "x": [0.5076824583866837, 0.510934393638171, 0.5122307548230005, 0.5193602693602694, 0.522633744855967, 0.5303326810176126, 0.5315315315315315, 0.5664652567975831, 0.5684647302904564, 0.6228187919463087], "y": ["C30", "C21", "A07", "C51", "A44", "C32", "C40", "C62", "C37", "D06"]}],
                        {"height": 500, "template": {"data": {"bar": [{"error_x": {"color": "#2a3f5f"}, "error_y": {"color": "#2a3f5f"}, "marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "baxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "pie": [{"automargin": true, "type": "pie"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#EBF0F8"}, "line": {"color": "white"}}, "header": {"fill": {"color": "#C8D4E3"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#2a3f5f", "arrowhead": 0, "arrowwidth": 1}, "coloraxis": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#2a3f5f"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "#E5ECF6", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "light"}, "paper_bgcolor": "white", "plot_bgcolor": "#E5ECF6", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "radialaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"line": {"color": "#2a3f5f"}}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "caxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "title": {"x": 0.05}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "title": {"standoff": 15}, "zerolinecolor": "white", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "title": {"standoff": 15}, "zerolinecolor": "white", "zerolinewidth": 2}}}, "title": {"text": "Top 10 Opening Moves by Winning Percentage", "x": 0.5}, "width": 600, "xaxis": {"title": {"text": "Winning Percentage"}}, "yaxis": {"title": {"text": "Openings"}}},
                        {"responsive": true}
                    )
                };
                
            </script>
        </div>
</body>
</html>

### Statistical Analysis

In this section, we will answer the objective of this study using statistical analysis. To see whether the type opening used is related to the outcome of the game, a test for independence using *chi-square test for independence* should be performed.

### Chi-Square Test of Independence

The Chi-Square Test can be used to test of independence between two categorical variables.  Specifically, it tests whether the frequencies of one categorical variable differ across levels of another categorical variable.  In other words, it tests whether or not a statistically significant relationship exists between the two variables.

**Null Hypothesis, $H_0$:** The outcome of the game is independent of the type of opening used.

**Significance level, $\alpha$:** 0.05

If the chi-square test statistic is greater than the critical value, the null hypothesis not be accepted.

This test does not consider directionality, that is, it is not of relevance whether one variable is statistically greater than or less than the other variable. Rather, it will only test if one variable is related to the other, therefore, a two-sided test will be used.

To compute for the chi-square test statistic and critical value, `chi2_contigency` and `chi2` modules from `scipy.stats` will be used respectively.

`chi2_contigency` function requires a contingency table. A contingency table is a table where columns and rows are the nominal categorical variables that needs to be tested, table values are the frequencies of occurence. In this problem, the variables are the outcome of the game and the openings, thus, the contingency table have game outcomes as rows, openings as columns and values are the frequencies of occurence of outcome-opening combinations.

**Contingency Table**

Shown below is a portion of the contigency table used in this problem.

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
      <th>A00</th>
      <th>A01</th>
      <th>A02</th>
      <th>A03</th>
      <th>A04</th>
      <th>A05</th>
      <th>A07</th>
      <th>A08</th>
      <th>A09</th>
      <th>A10</th>
      <th>...</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Win</th>
      <td>2900</td>
      <td>1210</td>
      <td>741</td>
      <td>405</td>
      <td>5347</td>
      <td>1032</td>
      <td>13674</td>
      <td>192</td>
      <td>868</td>
      <td>1899</td>
      <td>...</td>
    </tr>
    <tr>
      <th>Lose</th>
      <td>2588</td>
      <td>1065</td>
      <td>842</td>
      <td>539</td>
      <td>4082</td>
      <td>667</td>
      <td>6859</td>
      <td>119</td>
      <td>678</td>
      <td>1866</td>
      <td>...</td>
    </tr>
  </tbody>
</table>
</div>

The computed chi-square test statistic using `chi2_contingency` is 9031.31. The formula for this computation is discussed [here](https://stattrek.com/chi-square-test/independence.aspx).

To compute the critical value, `chi2.ppf` requires the lower tail probability and the degree of freedom. Lower tail probability can be computed by subtracting the significance level from 1, which then results to 0.95. Degree of freedom, also known as the shape parameter, is equal to $(r-1)*(c-1)$ where $r$ and $c$ are the number of rows and columns respectively (2 and 496) and is computed to be 495. Using these values, the critical value can then be computed using the percent point function of `chi2`, which is equal to 547.87.

Since the chi-square test statistic (9031.31) is greather than the critical value (547.87), **the null hypothesis is not accepted**.

### Conclusion

The results show that there is a significant relationship, specifically dependency, between the outcome of a game and type of opening used based on Chi-Square Test for Interdependence. Thus, it can be concluded that the result of a chess game has a **significant dependency on the opening move**.

### Recommendation

This project worked under the assumption that opening moves can only be performed by the white player. To further refine the results of the study, openings by black should also be considered.

### References

[1] https://en.wikipedia.org/wiki/Chess_opening <br>
[2] https://www.statisticssolutions.com/chi-square-2/ <br>
[3] https://stattrek.com/chi-square-test/independence.aspx

### Final Notes

This project was co-authored by [Rosely Peña](linkedin.com/in/roselydpena) as part of our requirements in MS in Data Science's Applied Computational Statistics course. If you wish to have a copy of the data and the code used in this project, you may send me a message via [Linked in](https://www.linkedin.com/in/conolcedric).

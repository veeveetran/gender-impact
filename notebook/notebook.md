# **Gender Impact: Exploring the extent of gender imbalances in Genshin Impact's character roster**




## 1 Introduction

To what extent do female players of Genshin impact experience a lack of content catered for the female gaze?

Genshin Impact in a highly-acclaimed open-world action roleplaying game. Although it is free to play, it features paid options that feed into the luck-based mechanics used to collect visually appealing characters, earning an estimated [$2.3 and 3.7 billion](https://screenrant.com/genshin-impact-fortnite-gta5-first-year-revenue/) in revenue across all platforms in the first year of release. Since character collection is a major component of the game for combat (and a main source of revenue for the company), this analysis will be focused on the preferences of the playerbase and how they are (and are not) reflected in the characters released.

As of 2023, [45% of the playerbase self-identified as female](https://prioridata.com/data/genshin-impact-player-count/), many of which who prefer at least a variety of characters to choose from. However, there has been [criticism]('https://gamerant.com/genshin-impact-needs-more-male-characters/#:~:text=Genshin%20Impact%20players%20have%20expressed,total%20of%2093%20playable%20characters) from frustrated players regarding the lack of playable male characters (and the repeated use fanservice meant to appeal to a male audience). While female characters could be designed for the [female gaze](https://sartorialmagazine.com/lifestyle/2023/2/17/the-female-gaze), this analysis focuses on the lack of male characters since this gender imbalance has been the center of public discourse.

This analysis seeks to answer the following questions: 
   - What is the gender distribution overall for all characters, and has this changed over time?
   - For male characters that are available, do they provide a enough varied gameplay compared to female characters?
   - How accessible are male characters in the game compared to female? Are they concentrated only at higher rarities?                                                                 

## Data Collection

I initially found the Genshin Impact character data already collected and published on Kaggle (current as of August 2024), so the dataset I used in this analysis was pulled from Kaggle via their API. Later on, I found that the Genshin Impact character data was publicly available through the [Genshin Impact Fandom Wiki](https://genshin-impact.fandom.com/wiki/Character/List). In the long-term, I would pull directly from the Wiki to always have the latest information on characters. This character list was the only data source I used.

This tabular dataset features 83 characters, not including the default main characters and Aloy (who was part of a limited collaboration with Sony). It features both qualitative and quantitative data. However, my analysis focuses primarily on dates and qualitative data.

My original plan was completely different from this analysis. I initally planned to analyze data collected across the past year from my cats' Litter Robots. However, this data was not publicly accessible via a website or API, and there was no straightforward way for me to integrate a public dataset into an analysis of my Litter Robots given the timeline. I decided to pursue a dataset that I am familiar with and highlight a topic that I am passionate about.

Below is a sample of the raw data.


```python
import pandas as pd
import plotly.express as px
import plotly.io as pio
pio.renderers.default = "notebook"
import plotly 
plotly.offline.init_notebook_mode(connected=True) 
import numpy as np

import plotly.graph_objects as go
import numpy as np
import datetime
import os
```


<script type="text/javascript">
window.PlotlyConfig = {MathJaxConfig: 'local'};
if (window.MathJax && window.MathJax.Hub && window.MathJax.Hub.Config) {window.MathJax.Hub.Config({SVG: {font: "STIX-Web"}});}
if (typeof require !== 'undefined') {
require.undef("plotly");
requirejs.config({
    paths: {
        'plotly': ['https://cdn.plot.ly/plotly-2.35.2.min']
    }
});
require(['plotly'], function(Plotly) {
    window._Plotly = Plotly;
});
}
</script>




```python
df_raw = pd.read_csv('../data/raw/characters_dataset.csv')
df = df_raw.copy()
df = df[['character_name', 'star_rarity', 'region', 'vision',
       'weapon_type', 'release_date', 'model', 'limited']]
    
df['height'] = df['model'].str.split(' ', expand=True)[0]
df['gender'] = df['model'].str.split(' ', expand=True)[1]
df['release_year'] = df['release_date'].str.split('-', expand=True)[0]
df['star_rarity'] = np.where(df['star_rarity']==5, '5-Star', '4-Star')
df = df[df['character_name']!='Aloy']
df = df[~df['character_name'].str.contains('Traveler')]
df_raw.head()
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
      <th>character_name</th>
      <th>star_rarity</th>
      <th>region</th>
      <th>vision</th>
      <th>arkhe</th>
      <th>weapon_type</th>
      <th>release_date</th>
      <th>model</th>
      <th>constellation</th>
      <th>birthday</th>
      <th>...</th>
      <th>atk_1_20</th>
      <th>def_1_20</th>
      <th>ascension_special_stat</th>
      <th>special_0</th>
      <th>special_1</th>
      <th>special_2</th>
      <th>special_3</th>
      <th>special_4</th>
      <th>special_5</th>
      <th>special_6</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Albedo</td>
      <td>5</td>
      <td>Mondstadt</td>
      <td>Geo</td>
      <td>NaN</td>
      <td>Sword</td>
      <td>2020-12-23</td>
      <td>Medium Male</td>
      <td>Princeps Cretaceus</td>
      <td>13-Sep</td>
      <td>...</td>
      <td>20</td>
      <td>68</td>
      <td>Geo DMG Bonus</td>
      <td>0.00%</td>
      <td>0.00%</td>
      <td>7.20%</td>
      <td>14.40%</td>
      <td>14.40%</td>
      <td>21.60%</td>
      <td>28.80%</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alhaitham</td>
      <td>5</td>
      <td>Sumeru</td>
      <td>Dendro</td>
      <td>NaN</td>
      <td>Sword</td>
      <td>2023-01-18</td>
      <td>Tall Male</td>
      <td>Vultur Volans</td>
      <td>11-Feb</td>
      <td>...</td>
      <td>24</td>
      <td>60</td>
      <td>Dendro DMG Bonus</td>
      <td>0.00%</td>
      <td>0.00%</td>
      <td>7.20%</td>
      <td>14.40%</td>
      <td>14.40%</td>
      <td>21.60%</td>
      <td>28.80%</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Aloy</td>
      <td>5</td>
      <td>NaN</td>
      <td>Cryo</td>
      <td>NaN</td>
      <td>Bow</td>
      <td>2021-09-01</td>
      <td>Medium Female</td>
      <td>Nora Fortis</td>
      <td>04-Apr</td>
      <td>...</td>
      <td>18</td>
      <td>53</td>
      <td>Cryo DMG Bonus</td>
      <td>0.00%</td>
      <td>0.00%</td>
      <td>7.20%</td>
      <td>14.40%</td>
      <td>14.40%</td>
      <td>21.60%</td>
      <td>28.80%</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Amber</td>
      <td>4</td>
      <td>Mondstadt</td>
      <td>Pyro</td>
      <td>NaN</td>
      <td>Bow</td>
      <td>2020-09-28</td>
      <td>Medium Female</td>
      <td>Lepus</td>
      <td>10-Aug</td>
      <td>...</td>
      <td>19</td>
      <td>50</td>
      <td>ATK</td>
      <td>0.00%</td>
      <td>0.00%</td>
      <td>6.00%</td>
      <td>12.00%</td>
      <td>12.00%</td>
      <td>18.00%</td>
      <td>24.00%</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Arataki Itto</td>
      <td>5</td>
      <td>Inazuma</td>
      <td>Geo</td>
      <td>NaN</td>
      <td>Claymore</td>
      <td>2021-12-14</td>
      <td>Tall Male</td>
      <td>Taurus Iracundus</td>
      <td>01-Jun</td>
      <td>...</td>
      <td>18</td>
      <td>75</td>
      <td>CRIT Rate</td>
      <td>0.00%</td>
      <td>0.00%</td>
      <td>4.80%</td>
      <td>9.60%</td>
      <td>9.60%</td>
      <td>14.40%</td>
      <td>19.20%</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 89 columns</p>
</div>



## 3 Data Cleaning

One the first steps I took to clean the data was to decide which columns were needed for the scope of the analysis. The raw data contained 89 columns, and I kept 8.

I filtered any characters/variations of characters that players are unable to obtain through the luck-based mechanic. This includes multiple forms of the main character (male/female form and element associated with the main character) and Aloy, who came from a collaboration with Sony in 2021 and can no longer be obtained by new players. 

I also processed the text from the following columns:
- model: Contained gender and model body type. I performed a string split to grab only gender
- release_year: Extracted from full release_date string (I also grabbed release_month for a later visualization)
- star_rarity: This column was a number (either 4 or 5), so I converted it to a string ("4-Star" and "5-Star") since we are treating them as categories in this analysis

For a later visualization, I created a conditional field called "genders_released" that indicates genders of characters that were released in a given month (Female-only, Male-only, Female + Male). This is used to visualize the gaps in time between when male characters get rolled out (and compare them to the release schedule of female characters).

For each data visualization, I created a copy of the processed data, and I aggregated the data to get counts by groups (and their corresponding percentages).

Below is a sample of the processed data.


```python
df.head()
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
      <th>character_name</th>
      <th>star_rarity</th>
      <th>region</th>
      <th>vision</th>
      <th>weapon_type</th>
      <th>release_date</th>
      <th>model</th>
      <th>limited</th>
      <th>height</th>
      <th>gender</th>
      <th>release_year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Albedo</td>
      <td>5-Star</td>
      <td>Mondstadt</td>
      <td>Geo</td>
      <td>Sword</td>
      <td>2020-12-23</td>
      <td>Medium Male</td>
      <td>True</td>
      <td>Medium</td>
      <td>Male</td>
      <td>2020</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alhaitham</td>
      <td>5-Star</td>
      <td>Sumeru</td>
      <td>Dendro</td>
      <td>Sword</td>
      <td>2023-01-18</td>
      <td>Tall Male</td>
      <td>True</td>
      <td>Tall</td>
      <td>Male</td>
      <td>2023</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Amber</td>
      <td>4-Star</td>
      <td>Mondstadt</td>
      <td>Pyro</td>
      <td>Bow</td>
      <td>2020-09-28</td>
      <td>Medium Female</td>
      <td>False</td>
      <td>Medium</td>
      <td>Female</td>
      <td>2020</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Arataki Itto</td>
      <td>5-Star</td>
      <td>Inazuma</td>
      <td>Geo</td>
      <td>Claymore</td>
      <td>2021-12-14</td>
      <td>Tall Male</td>
      <td>True</td>
      <td>Tall</td>
      <td>Male</td>
      <td>2021</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Arlecchino</td>
      <td>5-Star</td>
      <td>Snezhnaya</td>
      <td>Pyro</td>
      <td>Polearm</td>
      <td>2024-04-24</td>
      <td>Tall Female</td>
      <td>True</td>
      <td>Tall</td>
      <td>Female</td>
      <td>2024</td>
    </tr>
  </tbody>
</table>
</div>



## 4 Data Analysis

For any sort of analysis, I approach the dataset from a "high to low" perspective: I start with very broad exploration at a high level and drill down to certain details that look interesting later on. This allows me to "peel the layers" of the dataset and uncover insights while always having context at a high level.

### 1. Quantifying the overall gap between female and male characters
- 64% of characters are female while 36% are male
- The percentage of male characters have decreased overall every year since the game launched. However, in 2023, the 53% of characters released were male while 47% were female. This is the only year where male characters had a higher percentage. Even though the data is only current up until August 2024, only two additional male characters have been released (along with new female characters) from then until now.

### 2. Identifying areas key areas of improvement
- Each character possesses an element and weapon type that corresponds with their combat abilities
- Elements: There were no elements where males outnumbered females. Most elements at least had a percentage of males that outnumbered the percentage of males in the overall population (36%). Anemo was the most balanced element with 55% female and 46% male, while Electro was least balanced with 77% felame amd 23% male. Pyro and Hydro both had 33% males.
- Weapons: There were no weapons where where males outnumbered females. However, claymore-wielding characters had the most balanced gender distribution with 53% female and 47% male. Catalyst wielders were the least balance with 71% female and 29% male.


### 3. Assessing accessibility of female and male characters
- There are two levels of rarity for characters: 4-stars and 5-stars. 5-stars have better combat stats, but they have a lower probability of being pulled (especially without players spending real-life currency).
- The balance between 4-stars and 5-stars overall is very balanced with 48% of characters being 4-stars and 52% being 5-stars.
- This balance is not reflected when drilling down to gender. Female characters are balanced with 51% 4-stars and 49% 5-stars. Male characters had 43% 4-stars and 57% 5-stars. On top of being released less frequently, male characters are overall more difficult to obtain for free-to-play and low-spenders.
- The longest gap between new male characters being released is 4 months, while the longest gap between new female characters being released is 2 months, indicating that players who want make characters typically have to wait longer between releases.

## 5 Visualizations

Each visualization corresponds to a key finding.

### 1. Quantifying the overall gap between female and male characters

I used bar charts for for these visuals because they are an efficient way to display many categories in one view while being digestible.

The colors indicate the gender while the bars indicate count of characters. I included labels to also share percentage breakdown.


```python
df_grouped=df.groupby('gender').count().reset_index()
df_grouped = df_grouped[['gender', 'character_name']].rename(columns={'character_name':'count'})
df_grouped['percentage'] = round(df_grouped['count']/df_grouped['count'].sum()*100,1)

fig = px.bar(df_grouped,
            x='count',
            y='gender',
            title='Female vs Male Character Counts 2020 - 2024',
            color='gender',
            hover_data=['gender', 'count', 'percentage'],
            barmode='stack', width=800, height=600, text_auto=False,
            text=['{} ({:.0%})'.format(v, p/100) for v,p in zip(df_grouped['count'], df_grouped['percentage'])],
            color_discrete_map={
                'Male': '#2280B8',
                'Female': '#F29A8B'},
            orientation='h'
            )

fig.update_traces(width = .2)
fig.update_layout( plot_bgcolor='#F1EEEE')
# plot
fig.show()
```


<div>                            <div id="a3ff6c94-03e8-41cb-8f63-87dd726d26c9" class="plotly-graph-div" style="height:600px; width:800px;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("a3ff6c94-03e8-41cb-8f63-87dd726d26c9")) {                    Plotly.newPlot(                        "a3ff6c94-03e8-41cb-8f63-87dd726d26c9",                        [{"alignmentgroup":"True","customdata":[[63.9]],"hovertemplate":"gender=%{y}\u003cbr\u003ecount=%{x}\u003cbr\u003etext=%{text}\u003cbr\u003epercentage=%{customdata[0]}\u003cextra\u003e\u003c\u002fextra\u003e","legendgroup":"Female","marker":{"color":"#F29A8B","pattern":{"shape":""}},"name":"Female","offsetgroup":"Female","orientation":"h","showlegend":true,"text":["53 (64%)"],"textposition":"auto","x":[53],"xaxis":"x","y":["Female"],"yaxis":"y","type":"bar","width":0.2},{"alignmentgroup":"True","customdata":[[36.1]],"hovertemplate":"gender=%{y}\u003cbr\u003ecount=%{x}\u003cbr\u003etext=%{text}\u003cbr\u003epercentage=%{customdata[0]}\u003cextra\u003e\u003c\u002fextra\u003e","legendgroup":"Male","marker":{"color":"#2280B8","pattern":{"shape":""}},"name":"Male","offsetgroup":"Male","orientation":"h","showlegend":true,"text":["30 (36%)"],"textposition":"auto","x":[30],"xaxis":"x","y":["Male"],"yaxis":"y","type":"bar","width":0.2}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"xaxis":{"anchor":"y","domain":[0.0,1.0],"title":{"text":"count"}},"yaxis":{"anchor":"x","domain":[0.0,1.0],"title":{"text":"gender"},"categoryorder":"array","categoryarray":["Male","Female"]},"legend":{"title":{"text":"gender"},"tracegroupgap":0},"title":{"text":"Female vs Male Character Counts 2020 - 2024"},"barmode":"stack","height":600,"width":800,"plot_bgcolor":"#F1EEEE"},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('a3ff6c94-03e8-41cb-8f63-87dd726d26c9');
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

                        })                };                });            </script>        </div>


Bar charts are also an effective way to observe changes over time between categories.


```python
# has the gender distribution changed over time?
df_grouped=df.groupby(['release_year', 'gender']).count().reset_index()
df_grouped = df_grouped[['release_year','gender', 'character_name']].rename(columns={'character_name':'count'})
df_grouped['percentage'] = round(df_grouped['count']/df_grouped.groupby(['release_year'])['count'].transform('sum')*100,1)
fig = px.bar(df_grouped, x='release_year', y='count',
             title='Female vs Male Character Counts over Time, 2020 - 2024',
             hover_data=['gender', 'count', 'percentage'], color='gender',
             barmode='group', width=800, height=600, text_auto=False,
             text=['{} ({:.0%})'.format(v, p/100) for v,p in zip(df_grouped['count'], df_grouped['percentage'])],
             color_discrete_map={'Male': '#2280B8','Female': '#F29A8B'}
                )
fig.update_traces(textposition='outside')
fig.update_layout( plot_bgcolor='#F1EEEE')
fig.show()
```


<div>                            <div id="94e503bb-9967-4c82-9382-8e0dc33fc9b1" class="plotly-graph-div" style="height:600px; width:800px;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("94e503bb-9967-4c82-9382-8e0dc33fc9b1")) {                    Plotly.newPlot(                        "94e503bb-9967-4c82-9382-8e0dc33fc9b1",                        [{"alignmentgroup":"True","customdata":[["Female",61.5],["Female",68.8],["Female",70.6],["Female",46.7],["Female",77.8]],"hovertemplate":"gender=%{customdata[0]}\u003cbr\u003erelease_year=%{x}\u003cbr\u003ecount=%{y}\u003cbr\u003etext=%{text}\u003cbr\u003epercentage=%{customdata[1]}\u003cextra\u003e\u003c\u002fextra\u003e","legendgroup":"Female","marker":{"color":"#F29A8B","pattern":{"shape":""}},"name":"Female","offsetgroup":"Female","orientation":"v","showlegend":true,"text":["16 (62%)","11 (69%)","12 (71%)","7 (47%)","7 (78%)"],"textposition":"outside","x":["2020","2021","2022","2023","2024"],"xaxis":"x","y":[16,11,12,7,7],"yaxis":"y","type":"bar"},{"alignmentgroup":"True","customdata":[["Male",38.5],["Male",31.2],["Male",29.4],["Male",53.3],["Male",22.2]],"hovertemplate":"gender=%{customdata[0]}\u003cbr\u003erelease_year=%{x}\u003cbr\u003ecount=%{y}\u003cbr\u003etext=%{text}\u003cbr\u003epercentage=%{customdata[1]}\u003cextra\u003e\u003c\u002fextra\u003e","legendgroup":"Male","marker":{"color":"#2280B8","pattern":{"shape":""}},"name":"Male","offsetgroup":"Male","orientation":"v","showlegend":true,"text":["10 (38%)","5 (31%)","5 (29%)","8 (53%)","2 (22%)"],"textposition":"outside","x":["2020","2021","2022","2023","2024"],"xaxis":"x","y":[10,5,5,8,2],"yaxis":"y","type":"bar"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"xaxis":{"anchor":"y","domain":[0.0,1.0],"title":{"text":"release_year"}},"yaxis":{"anchor":"x","domain":[0.0,1.0],"title":{"text":"count"}},"legend":{"title":{"text":"gender"},"tracegroupgap":0},"title":{"text":"Female vs Male Character Counts over Time, 2020 - 2024"},"barmode":"group","height":600,"width":800,"plot_bgcolor":"#F1EEEE"},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('94e503bb-9967-4c82-9382-8e0dc33fc9b1');
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

                        })                };                });            </script>        </div>


### 2. Identifying areas key areas of improvement

I also opted for bar charts for these visuals, using the same color mapping for gender. These visualizations feature stacked bars for conciseness.


```python
# Is there a group with the most equal distribution? Are they concentrated in certain weapons? Vision types?
df_grouped = df.groupby(['vision', 'gender']).count().reset_index()
df_grouped = df_grouped[['vision', 'gender', 'character_name']].rename(columns={'character_name':'count'})
df_grouped['percentage'] = round(df_grouped['count']/df_grouped.groupby(['vision'])['count'].transform('sum')*100,1)

df_grouped
fig = px.bar(df_grouped, x="count", y="vision", color="gender", hover_data=['gender', 'count', 'percentage'],
            title = 'Comparing Gender Distribution Across Elements, 2020 - 2024',
            width=800, height=600, text_auto=False,
            text=['{} ({:.0%})'.format(v, p/100) for v,p in zip(df_grouped['count'], df_grouped['percentage'])],
            color_discrete_map={
                'Male': '#2280B8',
                'Female': '#F29A8B'},
            orientation='h',
             labels={
                     "vision": "Element"}

)

fig.update_traces(width = .5)
fig.update_layout( plot_bgcolor='#F1EEEE')
fig.update_layout(yaxis={'categoryorder': 'total ascending'})


fig.show()
```


<div>                            <div id="1f94aff0-dd33-4085-bc9b-e991b08678ed" class="plotly-graph-div" style="height:600px; width:800px;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("1f94aff0-dd33-4085-bc9b-e991b08678ed")) {                    Plotly.newPlot(                        "1f94aff0-dd33-4085-bc9b-e991b08678ed",                        [{"alignmentgroup":"True","customdata":[["Female",54.5],["Female",64.3],["Female",55.6],["Female",76.9],["Female",55.6],["Female",66.7],["Female",66.7]],"hovertemplate":"gender=%{customdata[0]}\u003cbr\u003ecount=%{x}\u003cbr\u003eElement=%{y}\u003cbr\u003etext=%{text}\u003cbr\u003epercentage=%{customdata[1]}\u003cextra\u003e\u003c\u002fextra\u003e","legendgroup":"Female","marker":{"color":"#F29A8B","pattern":{"shape":""}},"name":"Female","offsetgroup":"Female","orientation":"h","showlegend":true,"text":["6 (55%)","9 (64%)","5 (56%)","10 (77%)","5 (56%)","8 (67%)","10 (67%)"],"textposition":"auto","x":[6,9,5,10,5,8,10],"xaxis":"x","y":["Anemo","Cryo","Dendro","Electro","Geo","Hydro","Pyro"],"yaxis":"y","type":"bar","width":0.5},{"alignmentgroup":"True","customdata":[["Male",45.5],["Male",35.7],["Male",44.4],["Male",23.1],["Male",44.4],["Male",33.3],["Male",33.3]],"hovertemplate":"gender=%{customdata[0]}\u003cbr\u003ecount=%{x}\u003cbr\u003eElement=%{y}\u003cbr\u003etext=%{text}\u003cbr\u003epercentage=%{customdata[1]}\u003cextra\u003e\u003c\u002fextra\u003e","legendgroup":"Male","marker":{"color":"#2280B8","pattern":{"shape":""}},"name":"Male","offsetgroup":"Male","orientation":"h","showlegend":true,"text":["5 (46%)","5 (36%)","4 (44%)","3 (23%)","4 (44%)","4 (33%)","5 (33%)"],"textposition":"auto","x":[5,5,4,3,4,4,5],"xaxis":"x","y":["Anemo","Cryo","Dendro","Electro","Geo","Hydro","Pyro"],"yaxis":"y","type":"bar","width":0.5}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"xaxis":{"anchor":"y","domain":[0.0,1.0],"title":{"text":"count"}},"yaxis":{"anchor":"x","domain":[0.0,1.0],"title":{"text":"Element"},"categoryorder":"total ascending"},"legend":{"title":{"text":"gender"},"tracegroupgap":0},"title":{"text":"Comparing Gender Distribution Across Elements, 2020 - 2024"},"barmode":"relative","height":600,"width":800,"plot_bgcolor":"#F1EEEE"},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('1f94aff0-dd33-4085-bc9b-e991b08678ed');
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

                        })                };                });            </script>        </div>



```python
# Is there a element with the most equitable distribution? Are they concentrated in certain weapons? Vision types?
df_grouped = df.groupby(['weapon_type', 'gender']).count().reset_index()
df_grouped = df_grouped[['weapon_type', 'gender', 'character_name']].rename(columns={'character_name':'count'})
df_grouped['percentage'] = round(df_grouped['count']/df_grouped.groupby(['weapon_type'])['count'].transform('sum')*100,1)

df_grouped
fig = px.bar(df_grouped, x="count", y="weapon_type", color="gender", hover_data=['gender', 'count', 'percentage'],
            title = 'Comparing Gender Distribution Across Weapon Types, 2020 - 2024',
            width=800, height=600, text_auto=False,
            text=['{} ({:.0%})'.format(v, p/100) for v,p in zip(df_grouped['count'], df_grouped['percentage'])],
            color_discrete_map={
                'Male': '#2280B8',
                'Female': '#F29A8B'},
            orientation='h'

)

fig.update_traces(width = .4)
fig.update_layout( plot_bgcolor='#F1EEEE')
fig.update_layout(yaxis={'categoryorder': 'total ascending'})

fig.show()
```


<div>                            <div id="78131753-7523-4e14-8555-ab7ceee3984f" class="plotly-graph-div" style="height:600px; width:800px;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("78131753-7523-4e14-8555-ab7ceee3984f")) {                    Plotly.newPlot(                        "78131753-7523-4e14-8555-ab7ceee3984f",                        [{"alignmentgroup":"True","customdata":[["Female",62.5],["Female",70.6],["Female",53.3],["Female",68.8],["Female",63.2]],"hovertemplate":"gender=%{customdata[0]}\u003cbr\u003ecount=%{x}\u003cbr\u003eweapon_type=%{y}\u003cbr\u003etext=%{text}\u003cbr\u003epercentage=%{customdata[1]}\u003cextra\u003e\u003c\u002fextra\u003e","legendgroup":"Female","marker":{"color":"#F29A8B","pattern":{"shape":""}},"name":"Female","offsetgroup":"Female","orientation":"h","showlegend":true,"text":["10 (62%)","12 (71%)","8 (53%)","11 (69%)","12 (63%)"],"textposition":"auto","x":[10,12,8,11,12],"xaxis":"x","y":["Bow","Catalyst","Claymore","Polearm","Sword"],"yaxis":"y","type":"bar","width":0.4},{"alignmentgroup":"True","customdata":[["Male",37.5],["Male",29.4],["Male",46.7],["Male",31.2],["Male",36.8]],"hovertemplate":"gender=%{customdata[0]}\u003cbr\u003ecount=%{x}\u003cbr\u003eweapon_type=%{y}\u003cbr\u003etext=%{text}\u003cbr\u003epercentage=%{customdata[1]}\u003cextra\u003e\u003c\u002fextra\u003e","legendgroup":"Male","marker":{"color":"#2280B8","pattern":{"shape":""}},"name":"Male","offsetgroup":"Male","orientation":"h","showlegend":true,"text":["6 (38%)","5 (29%)","7 (47%)","5 (31%)","7 (37%)"],"textposition":"auto","x":[6,5,7,5,7],"xaxis":"x","y":["Bow","Catalyst","Claymore","Polearm","Sword"],"yaxis":"y","type":"bar","width":0.4}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"xaxis":{"anchor":"y","domain":[0.0,1.0],"title":{"text":"count"}},"yaxis":{"anchor":"x","domain":[0.0,1.0],"title":{"text":"weapon_type"},"categoryorder":"total ascending"},"legend":{"title":{"text":"gender"},"tracegroupgap":0},"title":{"text":"Comparing Gender Distribution Across Weapon Types, 2020 - 2024"},"barmode":"relative","height":600,"width":800,"plot_bgcolor":"#F1EEEE"},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('78131753-7523-4e14-8555-ab7ceee3984f');
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

                        })                };                });            </script>        </div>


### 3. Assessing accessibility of female and male characters

I opted for pie and suburst charts here because I wanted to emphasize the difference in proportions rather than raw counts (which we already covered in previous visualizations).

The colors corresponded to the character rarity levels.


```python
# what do character rarities look like? 
df_grouped=df.groupby(['star_rarity']).count().reset_index()
df_grouped = df_grouped[['star_rarity', 'character_name']].rename(columns={'character_name':'count'})
df_grouped['percentage'] = round(df_grouped['count']/df_grouped.groupby(['star_rarity'])['count'].transform('sum')*100,1)

fig = px.pie(
    df_grouped, 
    values='count', 
    names='star_rarity', 
    color = 'star_rarity', 
    width=600, 
    height=400, 
    title = 'Overall Count of Characters by Star-Rarity, 2020 - 2024',
    color_discrete_map={'4-Star':'#655A7C','5-Star':'#ffd166'}
)

fig.update_traces(textposition='inside', textinfo='percent+label', insidetextorientation = 'radial', textfont_size=10)

fig.show()
```


<div>                            <div id="7366cd3c-a2ba-448f-b44e-1b6f2f1f08bd" class="plotly-graph-div" style="height:400px; width:600px;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("7366cd3c-a2ba-448f-b44e-1b6f2f1f08bd")) {                    Plotly.newPlot(                        "7366cd3c-a2ba-448f-b44e-1b6f2f1f08bd",                        [{"customdata":[["4-Star"],["5-Star"]],"domain":{"x":[0.0,1.0],"y":[0.0,1.0]},"hovertemplate":"star_rarity=%{customdata[0]}\u003cbr\u003ecount=%{value}\u003cextra\u003e\u003c\u002fextra\u003e","labels":["4-Star","5-Star"],"legendgroup":"","marker":{"colors":["#655A7C","#ffd166"]},"name":"","showlegend":true,"values":[40,43],"type":"pie","textfont":{"size":10},"insidetextorientation":"radial","textinfo":"percent+label","textposition":"inside"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"legend":{"tracegroupgap":0},"title":{"text":"Overall Count of Characters by Star-Rarity, 2020 - 2024"},"height":400,"width":600},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('7366cd3c-a2ba-448f-b44e-1b6f2f1f08bd');
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

                        })                };                });            </script>        </div>


This sunburst chart shows the breakdown of character rarities within each gender group. I selected this chart because it efficiently highlights the imbalance of gender AND character rarity (specifically for male characters) in one visual.


```python
df_grouped = df.groupby(['star_rarity', 'gender']).count().reset_index()
df_grouped = df_grouped[['star_rarity', 'gender', 'character_name']].rename(columns={'character_name':'count'})
df_grouped['percentage'] = round(df_grouped['count']/df_grouped.groupby(['star_rarity'])['count'].transform('sum')*100,1)

fig = px.sunburst(
    df_grouped, 
    path=['gender', 'star_rarity'], 
    values='count', 
    color='star_rarity',
    width=600, 
    height=400, 
    title = 'Breakdown of Overall Character Rarities, 2020 - 2024',
    )
fig.update_traces(
    textinfo="label+percent parent",
    insidetextorientation='horizontal',
    textfont_size=10
)
color_mapping = {'Male': '#2280B8','Female': '#F29A8B', '4-Star':'#655A7C','5-Star':'#ffd166'}
fig.update_traces(marker_colors=[color_mapping[cat] for cat in fig.data[-1].labels])

fig.show()
```


<div>                            <div id="dfcb9957-5e82-421b-a31e-6e4c783a1780" class="plotly-graph-div" style="height:400px; width:600px;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("dfcb9957-5e82-421b-a31e-6e4c783a1780")) {                    Plotly.newPlot(                        "dfcb9957-5e82-421b-a31e-6e4c783a1780",                        [{"branchvalues":"total","customdata":[["4-Star"],["4-Star"],["5-Star"],["5-Star"],["(?)"],["(?)"]],"domain":{"x":[0.0,1.0],"y":[0.0,1.0]},"hovertemplate":"labels=%{label}\u003cbr\u003ecount=%{value}\u003cbr\u003eparent=%{parent}\u003cbr\u003eid=%{id}\u003cbr\u003estar_rarity=%{customdata[0]}\u003cextra\u003e\u003c\u002fextra\u003e","ids":["Female\u002f4-Star","Male\u002f4-Star","Female\u002f5-Star","Male\u002f5-Star","Female","Male"],"labels":["4-Star","4-Star","5-Star","5-Star","Female","Male"],"marker":{"colors":["#655A7C","#655A7C","#ffd166","#ffd166","#F29A8B","#2280B8"]},"name":"","parents":["Female","Male","Female","Male","",""],"values":[27,13,26,17,53,30],"type":"sunburst","textfont":{"size":10},"insidetextorientation":"horizontal","textinfo":"label+percent parent"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"legend":{"tracegroupgap":0},"title":{"text":"Breakdown of Overall Character Rarities, 2020 - 2024"},"height":400,"width":600},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('dfcb9957-5e82-421b-a31e-6e4c783a1780');
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

                        })                };                });            </script>        </div>


To illustrate gaps between new characters, I decided to create a heatmap. The x-axis contains release month (using month number) and the y-axis contains release year.

Each rectangle has a color that indicates the genders released that month: 1 = Female Only, 2 = Male Only, 3 = Female and Male. Boxes with no color (i.e., grey) mean that no new characters were released.

I decided to use this visual because I wanted to highlight how frequently new female characters get released vs males.

I struggled with creating a custom mapping for discrete values. Given more time, I would have improved the color scale to show discrete values rather than continuous.


```python

df_cal = df.copy()
df_cal['release_date'] = pd.to_datetime(df_cal['release_date'])
df_cal['release_month'] = df_cal['release_date'].dt.strftime('%m')

# check if month has both male and female characters released
df_cal['genders_released'] = df_cal.groupby(['release_year', 'release_month'])['gender'].transform(lambda x: 'Male + Female' if set(['Male', 'Female']).issubset(x) else np.nan)

df_cal['genders_released1'] = df_cal.groupby(['release_year', 'release_month'])['gender'].transform(lambda x: 'Male Only' if x.nunique() == 1 and x.iloc[0] == 'Male' else np.nan)
df_cal['genders_released2'] = df_cal.groupby(['release_year', 'release_month'])['gender'].transform(lambda x: 'Female Only' if x.nunique() == 1 and x.iloc[0] == 'Female' else np.nan)

df_cal['genders_released'] = df_cal['genders_released'].fillna(df_cal['genders_released1'])
df_cal['genders_released'] = df_cal['genders_released'].fillna(df_cal['genders_released2'])
df_cal.drop(['genders_released1', 'genders_released2'], axis=1, inplace=True)
df_cal_grouped = df_cal.groupby(['release_year','release_month', 'genders_released']).count().reset_index()
df_cal_grouped = df_cal_grouped[['release_year','release_month', 'genders_released','character_name']].rename(columns={'character_name':'count'})

df_cal_grouped['gender_released_code'] = np.where(df_cal_grouped['genders_released']=='Male + Female', 3, df_cal_grouped['genders_released'] )
df_cal_grouped['gender_released_code'] = np.where(df_cal_grouped['gender_released_code'] =='Male Only', 2,  df_cal_grouped['gender_released_code'] )
df_cal_grouped['gender_released_code'] = np.where(df_cal_grouped['genders_released']=='Female Only', 1, df_cal_grouped['gender_released_code'] )


release_year = list(df_cal_grouped['release_year'])

base = datetime.datetime.today()
release_month = list(df_cal_grouped['release_month'])
z = list(df_cal_grouped['gender_released_code'])

fig = go.Figure(data=go.Heatmap(
        z=z,
        x=release_month,
        y=release_year,
        colorscale='ylgnbu', hoverinfo=['y', 'x', ]))


fig.update_layout(
    title=dict(text='Genders Released per Month (Color Code: 1 = F Only, 2 = M Only, 3 = F + M)'),
    xaxis_nticks=36)

fig.update_layout(hovermode="x")

fig.update_layout(xaxis={'categoryorder': 'category ascending'})
fig.update_layout(yaxis={'categoryorder': 'category descending'})

fig.show()

```


<div>                            <div id="c217abbc-0fa9-4f0e-bef4-53a970437bc3" class="plotly-graph-div" style="height:525px; width:100%;"></div>            <script type="text/javascript">                require(["plotly"], function(Plotly) {                    window.PLOTLYENV=window.PLOTLYENV || {};                                    if (document.getElementById("c217abbc-0fa9-4f0e-bef4-53a970437bc3")) {                    Plotly.newPlot(                        "c217abbc-0fa9-4f0e-bef4-53a970437bc3",                        [{"colorscale":[[0.0,"rgb(255,255,217)"],[0.125,"rgb(237,248,177)"],[0.25,"rgb(199,233,180)"],[0.375,"rgb(127,205,187)"],[0.5,"rgb(65,182,196)"],[0.625,"rgb(29,145,192)"],[0.75,"rgb(34,94,168)"],[0.875,"rgb(37,52,148)"],[1.0,"rgb(8,29,88)"]],"hoverinfo":["y","x"],"x":["09","10","11","12","01","02","03","04","05","06","07","08","09","11","12","01","02","03","05","06","07","08","09","10","11","12","01","03","05","08","09","10","11","12","01","03","04","06","08"],"y":["2020","2020","2020","2020","2021","2021","2021","2021","2021","2021","2021","2021","2021","2021","2021","2022","2022","2022","2022","2022","2022","2022","2022","2022","2022","2022","2023","2023","2023","2023","2023","2023","2023","2023","2024","2024","2024","2024","2024"],"z":[3,1,3,3,1,2,1,1,1,2,1,1,1,2,2,1,1,2,1,1,2,3,3,1,1,3,3,3,3,3,2,2,1,1,3,1,1,3,1],"type":"heatmap"}],                        {"template":{"data":{"histogram2dcontour":[{"type":"histogram2dcontour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"choropleth":[{"type":"choropleth","colorbar":{"outlinewidth":0,"ticks":""}}],"histogram2d":[{"type":"histogram2d","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmap":[{"type":"heatmap","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"heatmapgl":[{"type":"heatmapgl","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"contourcarpet":[{"type":"contourcarpet","colorbar":{"outlinewidth":0,"ticks":""}}],"contour":[{"type":"contour","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"surface":[{"type":"surface","colorbar":{"outlinewidth":0,"ticks":""},"colorscale":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]]}],"mesh3d":[{"type":"mesh3d","colorbar":{"outlinewidth":0,"ticks":""}}],"scatter":[{"fillpattern":{"fillmode":"overlay","size":10,"solidity":0.2},"type":"scatter"}],"parcoords":[{"type":"parcoords","line":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolargl":[{"type":"scatterpolargl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"bar":[{"error_x":{"color":"#2a3f5f"},"error_y":{"color":"#2a3f5f"},"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"bar"}],"scattergeo":[{"type":"scattergeo","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterpolar":[{"type":"scatterpolar","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"histogram":[{"marker":{"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"histogram"}],"scattergl":[{"type":"scattergl","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatter3d":[{"type":"scatter3d","line":{"colorbar":{"outlinewidth":0,"ticks":""}},"marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattermapbox":[{"type":"scattermapbox","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scatterternary":[{"type":"scatterternary","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"scattercarpet":[{"type":"scattercarpet","marker":{"colorbar":{"outlinewidth":0,"ticks":""}}}],"carpet":[{"aaxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"baxis":{"endlinecolor":"#2a3f5f","gridcolor":"white","linecolor":"white","minorgridcolor":"white","startlinecolor":"#2a3f5f"},"type":"carpet"}],"table":[{"cells":{"fill":{"color":"#EBF0F8"},"line":{"color":"white"}},"header":{"fill":{"color":"#C8D4E3"},"line":{"color":"white"}},"type":"table"}],"barpolar":[{"marker":{"line":{"color":"#E5ECF6","width":0.5},"pattern":{"fillmode":"overlay","size":10,"solidity":0.2}},"type":"barpolar"}],"pie":[{"automargin":true,"type":"pie"}]},"layout":{"autotypenumbers":"strict","colorway":["#636efa","#EF553B","#00cc96","#ab63fa","#FFA15A","#19d3f3","#FF6692","#B6E880","#FF97FF","#FECB52"],"font":{"color":"#2a3f5f"},"hovermode":"closest","hoverlabel":{"align":"left"},"paper_bgcolor":"white","plot_bgcolor":"#E5ECF6","polar":{"bgcolor":"#E5ECF6","angularaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"radialaxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"ternary":{"bgcolor":"#E5ECF6","aaxis":{"gridcolor":"white","linecolor":"white","ticks":""},"baxis":{"gridcolor":"white","linecolor":"white","ticks":""},"caxis":{"gridcolor":"white","linecolor":"white","ticks":""}},"coloraxis":{"colorbar":{"outlinewidth":0,"ticks":""}},"colorscale":{"sequential":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"sequentialminus":[[0.0,"#0d0887"],[0.1111111111111111,"#46039f"],[0.2222222222222222,"#7201a8"],[0.3333333333333333,"#9c179e"],[0.4444444444444444,"#bd3786"],[0.5555555555555556,"#d8576b"],[0.6666666666666666,"#ed7953"],[0.7777777777777778,"#fb9f3a"],[0.8888888888888888,"#fdca26"],[1.0,"#f0f921"]],"diverging":[[0,"#8e0152"],[0.1,"#c51b7d"],[0.2,"#de77ae"],[0.3,"#f1b6da"],[0.4,"#fde0ef"],[0.5,"#f7f7f7"],[0.6,"#e6f5d0"],[0.7,"#b8e186"],[0.8,"#7fbc41"],[0.9,"#4d9221"],[1,"#276419"]]},"xaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"yaxis":{"gridcolor":"white","linecolor":"white","ticks":"","title":{"standoff":15},"zerolinecolor":"white","automargin":true,"zerolinewidth":2},"scene":{"xaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"yaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2},"zaxis":{"backgroundcolor":"#E5ECF6","gridcolor":"white","linecolor":"white","showbackground":true,"ticks":"","zerolinecolor":"white","gridwidth":2}},"shapedefaults":{"line":{"color":"#2a3f5f"}},"annotationdefaults":{"arrowcolor":"#2a3f5f","arrowhead":0,"arrowwidth":1},"geo":{"bgcolor":"white","landcolor":"#E5ECF6","subunitcolor":"white","showland":true,"showlakes":true,"lakecolor":"white"},"title":{"x":0.05},"mapbox":{"style":"light"}}},"title":{"text":"Genders Released per Month (Color Code: 1 = F Only, 2 = M Only, 3 = F + M)"},"xaxis":{"nticks":36,"categoryorder":"category ascending"},"hovermode":"x","yaxis":{"categoryorder":"category descending"}},                        {"responsive": true}                    ).then(function(){

var gd = document.getElementById('c217abbc-0fa9-4f0e-bef4-53a970437bc3');
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

                        })                };                });            </script>        </div>


## 6 Conclusion

The data confirms that there truly is an imbalance in how characters are released.

With 45% of the playerbase being female but only 36% of characters being male, this highlights how a section of the playerbase feels unseen. This gap has also gotten worse over time. There are specific areas of gameplay that could benefit from more diversity. Electro, Pyro, and Hydro elements could include more male characters, and the same can be said for catalyst-wielders. The male characters released so far have not been accessible for free and low-spenders. This discourages a large portion of the playerbase who do not have expendable income for obtaining these characters.

Overall, with the decreasing trend with male character releases, there are players who may decide to leave the game to play other games that may cater more to their preferences.

## 7 Future Work
Given more time, I would incorporate additional data sources. I am particularly interested in character revenue data. One potential argument against releasing male characters is that they might not sell as well as female characters. However, this may not neccesarily be the case. I would want to look at actual sales data to validate this.

Additionally, I would want to gather data on the spending level of the playerbase and how this differs between genders. Considering that male characters are more difficult to obtain, this could alienate free-to-low spenders.

Finally, I am interested in gathering social media data (Twitter posts, Reddit posts, etc.) talking about gender imbalances in Genshin Impact. This would help highlight how the community is affected by the developer's decisions.

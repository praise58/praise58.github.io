---
name: Social Network Analysis of ego-Facebook
tools: [Python, Altair, vega-lite]
image: assets/pngs/cars.png
description: Interactive visualizations exploring friend degree distributions and subnetwork connectivity in the SNAP ego-Facebook dataset.
custom_js:
  - vega.min
  - vega-lite.min
  - vega-embed.min
  - justcharts
---

# Exploring the SNAP ego-Facebook Social Network

This project analyzes the combined social circles network from the Stanford Network Analysis Project (SNAP) **ego-Facebook** dataset, which captures anonymized friendship edges across 4,039 Facebook users and 88,234 connections.

## 1. Friend Count (Node Degree) Distribution

<vegachart schema-url="{{ site.baseurl }}/assets/json/hw5_facebook_plot1.json" style="width: 100%"></vegachart>

This first visualization displays the distribution of node degrees—the total number of Facebook friends each user has—across the combined ego-Facebook graph, distinguishing between regular network members ("Alters") and the 10 surveyed core users ("Ego Centers"). For encoding choices, node degree is mapped to the x-axis as a binned quantitative variable (`degree:Q`), user count is mapped to the y-axis as an aggregated quantitative count (`count():Q`) on a symmetric logarithmic (`symlog`) scale so that high-degree hubs remain visible alongside low-degree users, and user role (`node_type:N`) is encoded using nominal color. I chose a high-contrast two-tone categorical palette (`#4c78a8` steel blue for Alters and `#e45756` coral red for Ego Centers) so the small set of surveyed hub users stands out immediately against the thousands of regular nodes. On the data transformation side in Python, the raw space-delimited edge list was loaded directly from SNAP's gzipped URL, concatenated across both `source` and `target` columns to compute undirected node degrees via `value_counts()`, and merged with ego-membership labels derived by grouping edges connected to the 10 ego IDs.

## 2. Ego Subnetwork Size and Average Connectivity

<vegachart schema-url="{{ site.baseurl }}/assets/json/hw5_facebook_plot2.json" style="width: 100%"></vegachart>

This second visualization compares the 10 individual ego subnetworks by total user count while simultaneously encoding how tightly interconnected each subnetwork's members are. In this bar chart, each ego subnetwork (`ego_label:N`) is encoded on the x-axis as a nominal category sorted in descending order by size, subnetwork population (`total_members:Q`) is encoded on the y-axis as a quantitative variable, and the average friend count within each subnetwork (`mean_degree:Q`) is mapped to a color scale using the `viridis` colormap. I selected `viridis` because it is perceptually uniform and colorblind-safe, allowing viewers to see immediately that larger subnetworks (such as Ego 107 and Ego 1684) do not necessarily have the highest average internal connectivity compared to denser, mid-sized clusters like Ego 1912. To construct this dataset in Python, each node was assigned to its primary ego network using a minimum-ID grouping rule over edges incident to the 10 ego centers, followed by a `.groupby().agg()` summary computing total members, mean degree, and maximum degree per subnetwork.

## Interactivity Discussion

To go beyond standard pan and zoom, both visualizations incorporate targeted Altair selection mechanisms that clarify overlapping network patterns. In the first plot, an interactive `binding_select` dropdown menu (`ego_select`) filters the entire degree histogram by individual ego subnetwork (`ego_label`), allowing the viewer to isolate how a single user's friend circle compares against the global right-tailed distribution without visual clutter. In the second plot, a `selection_point` click interaction (`bar_select`) paired with conditional opacity dims unselected bars to 35% opacity while keeping the clicked subnetwork at full brightness alongside rich hover tooltips, making it much easier to compare specific subnetworks side-by-side.

## Data & Analysis Links

<div class="left">
{% include elements/button.html link="https://snap.stanford.edu/data/ego-Facebook.html" text="The Data" %}
</div>

<div class="right">
{% include elements/button.html link="https://github.com/praise58/praise58.github.io/blob/main/python_notebooks/hw5_ego_facebook.ipynb" text="The Analysis" %}
</div>
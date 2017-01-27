# Basic Demo<a name="basics"></a>

* *Degree of Difficulty*: *
* *Goal*: what US counties have higher risk for insuring railroad companies.
* *Features Highlighted*:
  * Widgets: Category, Formula and Time Series.
  * Analysis: Intersect, Outliers & Cluster analysis.
* *Datasests needed*:
  * Railroad accidents (*dot_rail_safety_data*): download it [from the `builder-demo` CARTO account](https://team.carto.com/u/ramirocartodb/tables/builder-demo.dot_rail_safety_data_1/public/map?redirected=true) and import it into CARTO from your local machine.
  * US counties (*cb_2013_us_county_500k*): search and connect via Data Library.

## Contents

<!-- MarkdownTOC -->

- 1. Import and create a map
- 2. Style layer
- 3. Add widgets
- 4. Add US counties layer, start the analysis
- 5. Continue the analysis, get outliers and clusters
- 6. Share and export your results

<!-- /MarkdownTOC -->


## 1. Getting started <a name="getting-started"></a>

### 1.1. Create a map <a name="map"></a>

* Click on **`NEW MAP`**.
* Clik on **`SEARCH`** and type "world borders".
* Select `world_borders` dataset.
* Click on **`CREATE MAP`**.
* You can rename the map and layer title as "Countries & Cities" and "Countries" respectively, doing double click over them.

<br>

![map](/imgs/basics/map.png)
<figcaption>A view of BUILDER main dashboard</figcaption>

<hr>

### 1.2. Layers <a name="layers"></a>

* Add a new layer:
  * Click on **`ADD`**.
  * Click on **`SEARCH`** and type "populated places".
  * Select `ne_10m_populated_places_simple` dataset.
  * Click on **`ADD LAYER`**.
* You can rename the title of this new layer as "Cities".
* Click on the layer to show its components: 
  * **`DATA`**
  * **`ANALYSIS`**
  * **`STYLE`**
  * **`POP-UP`**
  * **`LEGEND`**

<br>

![layer](/imgs/basics/layer.png)
<figcaption>A view of BUILDER layer main menu</figcaption>

<hr>

### 1.3. Styling <a name="styling"></a>

* Create a bubble (proportional symbols) map:
  * Click on **`STYLE`**.
  * Click on point-size number.
  * Select **`BY VALUE`**.
  * Select `pop_max` column.

<br>

![dots](/imgs/basics/dots.png)
<figcaption>A view of BUILDER bubble map</figcaption>

<br>

* Create a chroropleth map:
  * Click on `marker-fill` column.
  * Select **`BY VALUE`**.
  * Select `pop_max` column.
  * You can customize your map further changing (and flipping) a different color palette, the number of buckets and quantification method.

<br>

![choropleth](/imgs/basics/choropleth.png)
<figcaption>A view of BUILDER bubble & choropleth map</figcaption>

<br>

* To learn more about how this works behind the scenes check out the CartoCSS panel.

<hr>

### 1.4. Widgets <a name="widgets"></a>

* Add widgets to "Cities" layer:
  * Click on **`DATA`**.
  * Select `point count` in order to show the number of cities.
  * Select `name` in order to filter by city name.
  * Click on **`EDIT`** in order to customize both widgets.
* Add widgets to "Countries" layer:
  * Click on **`LAYERS`**.
  * Click on "Countries" layer.
  * Click on **`DATA`**.
  * Select `name` in order to filter by country name.
  * Click on **`EDIT`** in order to customize both widgets.
* Now you can filter and autostyle by country and city name.

<br>

![widgets](/imgs/basics/widgets.png)
<figcaption>A view of BUILDER widgets</figcaption>

<hr>

### 1.5. Analysis <a name="analysis"></a>

* Go back to the main menu.
* Click on **`ADD ANALYSIS`** just below "Cities".
* Select **`Filter by layer`** analysis.
* Click on **`ADD ANALYSIS`**.
* Set the parameters as follows:
  * `FILTER BY LAYER`: "Countries".
  * `SOURCE COLUMN`: `sov_a3`.
  * `FILTER COLUMN`: `iso3`.
* Now fitering by country, you are also filtering the cities within that country. 

<br>

![filter](/imgs/basics/filter.png)
<figcaption>A view of BUILDER filtering and analysis power</figcaption>

<hr>

### 1.6. Publish <a name="publish"></a>

* Click on **`SHARE`**.
* Set to `LINK` or `PUBLIC`.
* Click on **`PUBLISH`**.
* Now you can share the map as link or embed.

<br>

<iframe width="100%" height="520" frameborder="0" src="https://team.carto.com/u/ramirocartodb/builder/23cd250b-ffd9-4267-806f-b935716eeb8c/embed" allowfullscreen webkitallowfullscreen mozallowfullscreen oallowfullscreen msallowfullscreen></iframe>

---

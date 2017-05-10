# Valencia Bikes

* *Degree of Difficulty*: **
* *Goal*: Getting started with CARTO BUILDER.
* *Features Highlighted*:
  * Analysis
* *Datasests needed*. In this lesson we will connect the GeoJSON files provided at these pages to your account:
  - [Barrios](http://gobiernoabierto.valencia.es/en/resource/?ds=barrios&id=15befdeb-83ac-4b0f-98f6-0e17ea68ce37)
  - [Aparcabicis](http://gobiernoabierto.valencia.es/en/resource/?ds=aparcabicis&id=6c38de72-598c-4e83-a093-73b17b08bbe1)

## Contents

<!-- MarkdownTOC -->

- Create a map
- Adding a virtual field to a source layer
- Analysis 1: Filter the bike parkings inside the neighborhoods
- Analysis 2: Intersect bikes and neighborhoods
- Styling
- Widgets
- Legends and interactivity
- Map options and publish

<!-- /MarkdownTOC -->

## Create a map <a name="map"></a>

* Click on **`NEW MAP`**.
* Click on **`CONNECT DATASET`**. Submit this url, choose any `Sync my data` option if sync table feature is available for your account, and make sure the `Let CARTO automatically guess data types and content on import` box is checked: [http://mapas.valencia.es/lanzadera/opendata/Barrios/JSON](http://mapas.valencia.es/lanzadera/opendata/Barrios/JSON). Click **`CONNECT DATASET`** button.
* Click on **`CREATE MAP`**.
* From the map's **`LAYERS`** panel click **`ADD`**. Click **`CONNECT DATASET`**. Connect the `aparcabicis` dataset using the same steps as above, with this url: [http://mapas.valencia.es/lanzadera/opendata/aparcabicis/JSON](http://mapas.valencia.es/lanzadera/opendata/aparcabicis/JSON)
* Double-click on the map layer titles to rename them "Bike parking" and "Neighborhoods". You can also rename the map this way.

![map](imgs/bikes/01-load-layers.png)
<figcaption>A view of BUILDER main dashboard. Our layer order might be different than yours depending on which file you imported first.</figcaption>


## Adding a virtual field to a source layer <a name="addfield"></a>

* Select the original Bike parking (aparcabicis) map layer (`A0` or `B0`)
* Click on the **`DATA`** tab
* Switch the toggle to **`SQL`**
* Aparacibis dataset's `plaza` column contains the number of parking spots at each point location, but as a `string` (text). To turn that data into a number we can create a new integer-type plazas_int column by pasting the query below into the map layer's SQL panel. After clicking **`APPLY`** click the Map View's columns-icon button to switch to Data View. Check that `plazas_int` is there.

```sql
SELECT *,
       plazas::int as plazas_int
  FROM aparcabicis
```

## Analysis 1: Filter to find only bike parking inside the neighborhoods <a name="filter"></a>

* Click on the Neighborhoods layer to open it
* Click the layer's **`ANALYSIS`** tab, then click **`ADD ANALYSIS`**. Select a `Filter points in polygons` analysis and click **`ADD ANALYSIS`**
* Select the "Bike parking" layer as the `Filtering layer` and click **`APPLY`**
* Click the back arrow next to the Neighborhoods title to return to the main map panel. Hide the original Bike parking layer by clicking it's eye icon.
* Drag the (A1) or (B1) `Filter points in polygons` analysis out of the Neighborhoods layer card. Drop it to see the neighborhood polygons again. Drag this layer on top of the original Neighborhoods layer.
* Click on the **`WIDGETS`** tab, and **`ADD WIDGET`**. Check the box for `nombre` & click **`CONTINUE`** to add a Category widget for neighborhood names. Make a widget selection to check how it filters parking points in addition to neighborhood polygons. Make sure you choose a neighborhood that contains bike parking, like `CAMPANAR`.

![map](imgs/bikes/02-filter.png)
<figcaption>Filtering parking points by neighborhood</figcaption>


## Analysis 2: Intersect bikes and neighborhoods <a name="intersect"></a>

* Select the original Neighborhoods layer again
* Click it's **`ANALYSIS`** tab, and **`ADD ANALYSIS`**. Select the `Intersect second layer` analysis and click **`ADD ANALYSIS`**
* In the `INTERSECT LAYER` field, select the `Filter points in polygons` node
* In the `OPERATION` field choose `SUM` for the `plazas_int` column & click **`APPLY`** to find the total number of available parking spots.
* You should have a new `sum_plazas_int` field in your layer's table aggregating the number of parking spots.

![map](imgs/bikes/03-sum.png)
<figcaption>Aggregating data from points into polygons</figcaption>


## Styling <a name="style"></a>

* Create a choropleth map for the Neighborhoods polygon layer illustrating how many parking spots are available per neighborhood
* Create a bubble map for the point layer using the number of parking spots as a measure
* To learn more about how to do this and how this styling works behind the scenes, check out the CartoCSS panel and consider doing this [cartography workshop lesson](https://github.com/CartoDB/carto-workshop/tree/master/03-cartography).

![map](imgs/bikes/04-styling.png)
<figcaption>Styling the datasets</figcaption>


## Widgets <a name="widget"></a>

Consider adding the following widgets:

* formula widget for the sum of parking spots
* formula widget for the average of parking spots per point
* histogram for the parking spots
* category for the parking types
* category for the original neighborhoods dataset
* category for the aggregated neighborhoods dataset, so you can order by number of parking slots

![map](imgs/bikes/05-widgets.png)
<figcaption>Widgets layout</figcaption>


## Legends and interactivity <a name="legend"></a>

To finish the map you can work a bit on it's legends and interactivity (pop-ups).

* Customize legend titles and labels
* Set up a tooltip (hover pop-up) for the neighborhoods dataset and an infowindow (click pop-up) for the parking dataset.

![map](imgs/bikes/06-legends.png)
<figcaption>Legends and popups</figcaption>


## Map options and publish <a name="publish"></a>

* Go to the map options (second button down from the back button in blue side bar)
  * Remove the search box
  * Add a layer selector
  * Remove the side bar (toolbar)
* Publish the map
  * Click on **`SHARE`**.
  * Set privacy to `LINK` or `PUBLIC`.
  * Click on **`PUBLISH`**.
  * Now you can share the map as a [link](https://xurxosanz.carto.com/builder/e5e528b0-2f75-11e7-a6ef-0e3ebc282e83/embed) or embed it.


```html
<iframe width="100%" height="520" frameborder="0"
  src="https://xurxosanz.carto.com/builder/e5e528b0-2f75-11e7-a6ef-0e3ebc282e83/embed"
  allowfullscreen webkitallowfullscreen mozallowfullscreen
  oallowfullscreen msallowfullscreen>
</iframe>
```


![map](imgs/bikes/07-result.png)
<figcaption>Final result</figcaption>
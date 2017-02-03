# Market Analysis (Dominos Pizza) Demo

* *Degree of Difficulty*: **

* *Goal*: explore the sourrounding areas of Dominos Pizza store, getting key insighsts such as total population, total households and carless households.

* *Features Highlighted*:
  * Widgets: Category & Category.
  * Analysis: Create AOI & Enrich with DO.

* *Datasests needed*:
  * NYC blocks (*nyc_blocks*): download from [here](https://team.carto.com/u/builder-demo/tables/nyc_blocks/public) as csv file.
  * Dominos data (*dominos_data*): download from [here](https://team.carto.com/u/builder-demo/tables/dominos_data/public) as csv file.


## Contents


<!-- MarkdownTOC -->

- 1. Import and create a map
- 2. Layers and styles
- 3. Analysis
- 4. Add new layer and some widgets

<!-- /MarkdownTOC -->


## 1. Import and create a map

### 1. 1. Import datasets

* First, import *nyc_blocks* into CARTO, drag and dropping the file into Builder dashboard.
* Then import *dominos_data*.
* Select both datasets and click on *CREATE MAP*.

### 1. 2. Create a map

* Just after clicking on *CREATE MAP*, the Builder main menu will open up. Builder user interface (already on *LAYERS* pane) will show four layers (*Positron labels*, *dominos_data*, *nyc_blocks* and *Positron*), first and last ones correspond with the basemap, the other two are proper data layers.
* Rename the map as *Dominos Pizza Demo* and the two layers as *Pizza Stores* and *NYC blocks* respectively.

![1](https://cloud.githubusercontent.com/assets/5215798/17518721/55f08e8c-5e49-11e6-985e-d84e52daa0cc.png)

## 2. Layers and styles

### 2. 1. Layers

* Change to a basemap without labels.

* Each layer (but the basemap) have a dataset as layer source and a node id. For instance, the source of *Pizza Stores* is `dominos_data`, with *A0* as node id. This will be very important when adding analysis and widgets.

### 2. 2. Layer options, styles

* Click on *NYC blocks* layer.

* *DATA*:
  * This tab gives a general idea of each field/column in the layer, its name and its data type. We could also add them as widgets from here.
  * Click the slider button from *VALUES* to *SQL*. The SQL view allows more advanced users to manage data using SQL language.
  * Finally, it's easy to change from Map view to Data view by clicking on the button at the bottom right corner.

* *STYLE*:
  * *FILL*: click on the color bar, select *BY VALUE*, set `total_pop` as the variable and choose a nice color palette.
  * Change the *STROKE* to `0.2`.
  * Click the slider button from *VALUES* to *CARTOCSS*. The CartoCSS view allows more advanced users to style the layer in a more precise way.

![2](https://cloud.githubusercontent.com/assets/5215798/17518737/6577ab2e-5e49-11e6-84be-4e28c764fbbf.png)

## 3. Analysis

### 3. 1. Create Areas of Interest

* Back in the main menu, click on *ADD ANALYSIS* on the *Pizza Stores* layer.
* Select *Create areas of influence*. Click on *ADD ANALYSIS*.
* Set *DISTANCE* to *km* and *RADIUS* to `1`. Hit *APPLY*.
* An explanatory pop up will appear. Click on *DONE*.

The map should now look like the following:

![3-1](https://cloud.githubusercontent.com/assets/5215798/17518746/6c3e1b5a-5e49-11e6-80b3-9127b8c9afbf.png)

### 3. 2. Get population and household data from the Data Observatory

* Add another Analysis to the Analysis Chain by clicking the plus symbol (+) in *Your workflow*.
* Select *Enrich from Data Observatory*. Click on *ADD ANALYSIS*.

> the Data Observatory enrichment is the result of the geometry location and the measure requested.

* Set *NEW COL. NAME* to `population`. Select *United States* as *COUNTRY*, *Age and Gender* as *MEASUREMENT* and *Total Population* as *SEGMENTS*. Click *APPLY*.
* An explanatory pop up will appear. Click on *DONE*.
* Repeat steps above to add two more *Enrich from Data Observatory Analysis* with the following parameters:
  * `total_households`, *United States*, *Housing* and *Households*.
  * `carless_households`, *United States*, *Transportation* and *Car-free households*.

> **Warning**: if the buffers (or AOIs) are not showing right on the map, refresh!

* Go to table view in order to see the new three fields the analysis have created.

![3-2](https://cloud.githubusercontent.com/assets/5215798/17518748/6eb4c172-5e49-11e6-8dd3-433c690ba827.png)

* Improve the visualization styling the AOIs. Back to the *STYLE* tab:
  * *FILL*: click on the color bar, select *BY VALUE*, set `carless_households` as the variable and choose a nice color palette with some contrast to the one used for *NYC blocks* layer. Set the number of buckets to `5`.
  * Set *BLENDING* to *multiply*.

![3-3](https://cloud.githubusercontent.com/assets/5215798/17518751/707e4bcc-5e49-11e6-9206-ff566970f7e6.png)

## 4. Add new layer and some widgets

### 4. 1. Add new layer

* Click on *LAYERS*, *ADD* a third layer, again select the *dominos_data* dataset and rename it as *Pizza Stores Points*.
* *STYLE* this new layer, set the marker width according to `monthly_revenue` value, select a color (white would be good choice) and set the *STROKE* to `0.2`.

![4-1](https://cloud.githubusercontent.com/assets/5215798/17518753/721eeb3a-5e49-11e6-9e90-ba24880aeb5d.png)

### 4. 2. Add widgets

* Back in Builder main interface, open *WIDGETS* pane and click *ADD WIDGET*:

> From *Pizza Stores Points* layer, which should be *C0* node:

  * one *CATEGORY* widget for *store_address*,
  * three *FORMULA* Widgets for *pizzas_sold*, *customers_served*, and *deliveries_made*. Rename these Widgets and drag them into the desired order,
  * and one *HISTOGRAM* widget for *monthly_revenue*.

* Also add the following widgets:

> From *Pizza Stores* layer (should be node *A0*).

  * Three *HISTOGRAM* widgets for *population*, *total_household* and *carless_households*.

> Tip 1: for widgets that have values with units, such as *monthly_revenue* (a dollar amount), you may add a *Prefix* to the widget, in this case a `$` symbol.

> Tip 2: rename each widget, they'll look nicer :)

![4-2](https://cloud.githubusercontent.com/assets/5215798/17518755/73942ed0-5e49-11e6-961b-d5d9a7a9db8f.png)

> Use the filters according to an invented target (for instance, stores with lower monthly revenue but closer to households without cars).

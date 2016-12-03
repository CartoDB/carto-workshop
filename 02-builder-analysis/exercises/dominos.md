# Market Analysis (Dominos Pizza) Demo

* *Degree of Difficulty*: **

* *Goal*: explore the sourrounding areas of Dominos Pizza store, getting key insighsts such as total population, total households and carless households.

* *Features Highlighted*:
  * Widgets: Category & Category.
  * Analysis: Create AOI & Enrich with DO.

* *Datasests needed*:
  * NYC blocks (**`nyc_blocks`**): download from [here](https://team.carto.com/u/builder-demo/tables/nyc_blocks/public) as csv file.
  * Dominos data (**`dominos_data`**): download from [here](https://team.carto.com/u/builder-demo/tables/dominos_data/public) as csv file.


## Contents


<!-- MarkdownTOC -->

- 1. Import and create a map
  - 1. 1. Import datasets
  - 1. 2. Create a map
- 2. Layers and styles
  - 2. 1. Layers
  - 2. 2. Layer options, styles
- 3. Analysis
  - 3. 1. Create Areas of Interest
  - 3. 2. Get population and household data from the Data Observatory
- 4. Add new layer and some widgets
  - 4. 1. Add new layer
  - 4. 2. Add widgets

<!-- /MarkdownTOC -->


## 1. Import and create a map

### 1. 1. Import datasets

* First, import **`nyc_blocks`** into CARTO, dragging and dropping the file.
* Secondly, import **`dominos_data`** into CARTO.
* Then select both datasets and click on `CREATE MAP`.

### 1. 2. Create a map

* Just after clicking on `CREATE MAP`, the Builder main menu will open up. On the one hand, the Builder dashboard will show four layers ("Positron labels", "dominos_data", "nyc_blocks" and "Positron"), the first and last are the basemap, the the other two are proper layers. On the other hand, your visualization will consist on blue polygons and orange points representing NYC blocks and Dominos Pizza stores, respectively.
* Rename the map as **"Dominos Pizza Demo"** and the two layers as "Pizza Stores" and "NYC blocks" respectively.

![1](https://cloud.githubusercontent.com/assets/5215798/17518721/55f08e8c-5e49-11e6-985e-d84e52daa0cc.png)

## 2. Layers and styles

### 2. 1. Layers

* Change a basemap without labels in order to show the viewer that now you have two layers instead of three.

* Each layer (but the basemap) have a dataset layer source. For instance, the source of the "Pizza Stores" layer is **`dominos_data`**, aka `a0`. This will be very important when adding analysis and widgets.

### 2. 2. Layer options, styles

* Click on "NYC blocks".

* `DATA`:
  * This interface gives you a general idea of each field/column in the layer, its name and its data type but also from there you can add them as widgets.
  * Switch to `VALUES` to `SQL`. The SQL command line allows more advanced users to manage data in a more precise way.
  * Finally, it's easy to change from map view to dataset view.

* `STYLE`:
  * `FILL`: click on the color bar, select `BY VALUE`, set `total_pop` as the variable and choose a nice color palette.
  * Change the `STROKE` to `0.2`.
  * Switch to `VALUES` to `CARTOCSS`. The CartoCSS command line allows more advanced users to layer style in a more precise way.

![2](https://cloud.githubusercontent.com/assets/5215798/17518737/6577ab2e-5e49-11e6-84be-4e28c764fbbf.png)

## 3. Analysis

### 3. 1. Create Areas of Interest

* Back in the main menu, click on 'ADD ANALYSIS' on the "Pizza Stores" layer.
* Select `Create areas of influence`. Click on `ADD ANALYSIS`.
* Set `DISTANCE` to `km` and `RADIUS` to `1`. `APPLY`.
* An explanatory window would pop up. Click on `DONE`.

The map should now look like the following:

![3-1](https://cloud.githubusercontent.com/assets/5215798/17518746/6c3e1b5a-5e49-11e6-80b3-9127b8c9afbf.png)

### 3. 2. Get population and household data from the Data Observatory

* Add another Analysis to the Analysis Chain by clicking the plus in `Workflow`.
* Select `Enrich from Data Observatory`. Click on `ADD ANALYSIS`.

> the Data Observatory enrichment is the result of the geometry location and the measure requested.

* Set `NEW COL. NAME` to `population`. Select `United States` as `COUNTRY`, `Age and Gender` as `MEASUREMENT` and `Total Population` as `SEGMENTS`. Click on `APPLY`.
* An explanatory window would pop up. Click on `DONE`.
* Add two more `Enrich from Data Observatory Analysis` in the same way with the following parameters:
  * `total_households`, `United States`, `Housing` and `Households`.
  * `carless_households`, `United States`, `Transportation` and `Car-free households`.

> **Warning**: if the buffers (or AOIs) are not showing right on the map, refresh!

* Go to table view in order to see the new three fields the analysis have created.

![3-2](https://cloud.githubusercontent.com/assets/5215798/17518748/6eb4c172-5e49-11e6-8dd3-433c690ba827.png)

* Improve the visualization styling the AOIs. Back to the `STYLE` tab:
  * `FILL`: click on the color bar, select `BY VALUE`, set `carless_households` as the variable and choose a nice color palette with some contrast to the one used for "NYC blocks" layer. Set `buckets` to `5`.
  * Set `BLENDING` to `multiply`.

![3-3](https://cloud.githubusercontent.com/assets/5215798/17518751/707e4bcc-5e49-11e6-9206-ff566970f7e6.png)

## 4. Add new layer and some widgets

### 4. 1. Add new layer

* Click on `LAYERS`, `ADD` a third layer, again select the `dominos_data` dataset and rename it as "Pizza Stores Points".
* `STYLE` this new layer, set the marker width according to `monthly_revenue` value, select a color (white would be good choice) and set the `STROKE` to `0.2`.

![4-1](https://cloud.githubusercontent.com/assets/5215798/17518753/721eeb3a-5e49-11e6-9e90-ba24880aeb5d.png)

### 4. 2. Add widgets

* Back in the main menu, click on `WIDGETS`, then `ADD WiDGET`:

> All the following widget would take fields from "Pizza Stores Points" layer, aka `C0`.

  * one `Category` widget for `store_address`,
  * four `Formula` Widgets for `pizzas_sold`, `customers_served`, and `deliveries_made`. Rename these Widgets and drag them into the desired order,
  * and one `Histogram` widget for `monthly_revenue`.

* And finally, add the following widgets:

> All the following widget would take fields from "Pizza Stores" layer, aka `A0`.

  * Three `Histogram` widgets for `population`, `total_households` and `carless_households`.

> Tip 1: for widgets that have values with units, such as `monthly_revenue` (a dollar amount), you may add a Prefix to the Widget, in this case a `$`.

> Tip 2: rename each widget, they look nicer if you do.

![4-2](https://cloud.githubusercontent.com/assets/5215798/17518755/73942ed0-5e49-11e6-961b-d5d9a7a9db8f.png)

> Use the filters according to an invented target (for instance, stores with lower monthly revenue but closer to households without cars).

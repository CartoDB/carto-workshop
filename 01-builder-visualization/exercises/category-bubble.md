# Categories + Bubble visualization

* *Degree of Difficulty*: **

* *Goal*: Create a bivariate visualization with BUILDER

* *Features Highlighted*:
	* Style: Advanced use of *BY VALUE* visualizations
	* Widgets: Formulas + Histograms

* *Datasests needed*:
	* *ne_10m_populated_places_simple* dataset from CARTO [Data Library](https://carto.com/dataset/ne_10m_populated_places_simple)

## Contents

<!-- MarkdownTOC -->

- 1. Import and create map
- 2. Style the map
- 3. Widgets
- 4. Exercise

<!-- /MarkdownTOC -->


## 1. Import and create map

### 1. 1. Import from the Data Library

Look for the dataset named `ne_10m_populated_places_simple` on the Data Library and import it to your account, then you can create a map with it. Alternatively you can directly create a new map and then search for the dataset.

## 2. Style the map
### 2. 1. Categories visualization
* Select *NONE* in the *Aggregation* selector and go directly to *STYLE* tab

![01-cat-bub-01](imgs/01-cat-bub-01.png)

* Click on the FILL color and select *BY VALUE*. Then scroll down to find `featurecla` column. It has information about the kind of places there are.

![01-cat-bub-02](imgs/01-cat-bub-02.png)

* You'll see that a legend has automatically been generated
* Click to the layer name options (button with three dots next to the layer name) to change the layer name and see how it updates in the legend

![01-cat-bub-03](imgs/01-cat-bub-03.png)

* Now click on the number that sets the marker size and choose *BY VALUE*
* Use the search bar to find `pop_max` column

![01-cat-bub-04](imgs/01-cat-bub-04.png)

* Use the controls to set the number of buckets, classification method and minimum/maximum size.

![01-cat-bub-05](imgs/01-cat-bub-05.png)

* We could also add labels with the name of each place. Set *OVERLAP* option to `false` to avoid the labels overlapping each other.

![01-cat-bub-06](imgs/01-cat-bub-06.png)

## 3. Widgets
### 3. 1. Add and configure widgets to explore your data
* Go back to the map main menu and click on *WIDGETS* to open widget's pane
* Click on *ADD* to open widget's options

![01-cat-bub-07](imgs/01-cat-bub-07.png)

* From *CATEGORY* tab select `adm0name`
* Switch to *HISTOGRAM* tab and select `pop_max`
* On *FORMULA* tab select also `pop_max`

* Click on *CONTINUE*
* Click on each widget's name to see its options and set them as follows:

* For the **category** widget:

![01-cat-bub-08](imgs/01-cat-bub-08.png)

* For the **histogram** widget:

![01-cat-bub-09](imgs/01-cat-bub-09.png)

* The **formula** widget requires a bit more of attention, as we need to select which calculation will be done.
* In order to get the total population that lives in the cities in the current map view, we need to select `poo_max` as column and *SUM* as operation.

![01-cat-bub-10](imgs/01-cat-bub-10.png)

* On *WIDGETS* pane, we could reorder them

![01-cat-bub-11](imgs/01-cat-bub-11.png)

* And check the results. Note how the widgets react to the map view, as well as to filters applied through other widgets.

![01-cat-bub-12](imgs/01-cat-bub-12.png)

## 4. Exercise

Use the widgets to find the average population of the most populated places in Mexico. **Tip:** you may need to change some parameter from the widgets settings.
# Categories + Bubble visualization

* *Degree of Difficulty*: **

* *Goal*: Create a bivariate visualization with BUILDER

* *Features Highlighted*:
	* Style: Advanced use of `BY VALUE` visualizations
	* Widgets: Formulas + Histograms

* *Datasests needed*:
	* ne_10m_populated_places_simple dataset from CARTO [Data Library](https://carto.com/dataset/ne_10m_populated_places_simple)

<!-- MarkdownTOC -->

- 1. Import and create map
- 2. Style the map
- 3. Widgets

<!-- /MarkdownTOC -->


## 1. Import and create map
### 1. 1. Use the link above to download the dataset and import it to the account.
* Just drag and drop the file into the CARTO Maps interface and wait for the dataset to be imported. The map should be automatically created.

## 2. Style the map
### 2. 1. Categories visualization
* Select `NONE` in the aggregation selector and go directly to the `Style` section

![01-cat-bub-01](imgs/01-cat-bub-01.png)

* Click on the color bar and select `BY VALUE`. Then scroll down to find `featurecla` column. It has information about the kind of places there are.

![01-cat-bub-02](imgs/01-cat-bub-02.png)

* You'll see that a legend has automatically been generated
* Click on the three points next to the layer name to change the layer name and see how it updates in the legend

![01-cat-bub-03](imgs/01-cat-bub-03.png)

* Now click on the number that sets the marker size and choose `BY VALUE`
* Use the search bar to find `pop_max` column

![01-cat-bub-04](imgs/01-cat-bub-04.png)

* Use the controls to set the number of buckets, classification method and minimum/maximum size. 

![01-cat-bub-05](imgs/01-cat-bub-05.png)

* We could also add labels with the name of each place. Set `Overlap` option to `false` to avoid the labels overlap each other. 

![01-cat-bub-06](imgs/01-cat-bub-06.png)

## 3. Widgets
### 3. 1. Add and configure widgets to explore your data
* Go back to the layer list and click on `WIDGETS` tab

![01-cat-bub-07](imgs/01-cat-bub-07.png)

* From `CATEGORY` section select `adm0name`
* Switch to `HISTOGRAM` and select `pop_max`
* On `FORMULA` select also `pop_max`

* Click on continue
* Use the menu to enter each widget configuration and set it as follows: 

* For the **category** widget: 

![01-cat-bub-08](imgs/01-cat-bub-08.png)

* For the **histogram** widget: 

![01-cat-bub-09](imgs/01-cat-bub-09.png)

* The **formula** widget requires a bit more of attention, as we need to select which calculation will be done. 
* In order to get the total population that lives in the cities in the current map view, we need to select `poo_max` as column and `sum` as operation. 

![01-cat-bub-10](imgs/01-cat-bub-10.png)

* We could use the widget list to reorder them

![01-cat-bub-11](imgs/01-cat-bub-11.png)

* And check the results. Note how the widgets react to the map view, as well as to filters applied through other widgets. 

![01-cat-bub-12](imgs/01-cat-bub-12.png)

* **Exercise:** Use the widgets to find the average population of the most populated places in Mexico. **Tip:** you may need to change some parameter from the widgets settings. 
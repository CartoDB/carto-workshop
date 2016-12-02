# Sales Territories (Portland) Demo

* *Degree of Difficulty*: ***

* *Goal*: Find the best place to create a store near the customers.

* *Features Highlighted*:
	* Analysis: Cluster Analysis, Weight Centroid Analysis, Area of influence analysis and Filter Points in polygons analysis.
	* Widgets: Formula widget and histogram widget.

* *Datasests needed*:
	* Customer locations dataset **``customer_home_locations``** from [Sales Territories (Portland) Demo](https://team.carto.com/u/builder-demo/viz/39fd43f3-3d3b-4b71-8c13-895305ccc2b8/public_map).


## Instructions

### 1. Import and create map

#### 1. 1. Import ``customer_home_locations`` csv.

* Show how easy is to import files into CARTO! Explain the viewer the wide diversity of geodata supported in CARTO during the importing.

#### 1. 2. Dataset view

* Don't waste time with this. But show the `METDATA`-`SQL` switch and the `PREVIEW`.

#### 1. 3. Click on ``CREATE MAP`` from the ``customer_home_locations``.


#### 1. 4. Rename ``customer_home_locations`` to ``Customer home locations`` and change the title of the map to ``Sales Territories (Portland) Demo``.


> **Warning**: after renaming a layer a error could pop up saying "the map cannot be rendered", don't worry about this. Refresh the page and it will dissapear.


* You should have a dashboard like this:

![first](https://cloud.githubusercontent.com/assets/11177693/17555923/ba604862-5f11-11e6-9d23-1a077768664d.png)


### 2. Layers and styles

#### 2. 1. Show the new ordering of the layers in the Builder

* Change the basemap to the dark_matter (labels below) basemap to show the different basemaps options.

* Explain that each layer (but the basemap) have a dataset layer source. For instance, the source of the ``Customer home locations`` layer is **``customer_home_locations``**, aka ``A0``. This would be very important when adding analysis and widgets.

#### 2. 2. Show the different options layers have

> Comment that each layer has 5 options: `DATA`, `ANALYSES`, `STYLE`, `POP-UPS` and `LEGENDS`.

* `DATA`:
  * Explain that this interface gives a general view of the fields the layer, its name and its data type but also from there you can add them as widgets.
  * Switch to `VALUES` to `SQL`. Explain that the SQL command line allows more advanced users to manage data in a more precise way.
  * Finally, show how easy is to change from map view to dataset view.

* `STYLE`:
  * Change the fill color to ``#cc1035`` and set the size of the markers to 7.
  * Set the stroke of the points to 0.
  * Switch to `VALUES` to `CARTOCSS`. Explain that the CartoCSS command line allows more advanced users to layer style in a more precise way.


![style](https://cloud.githubusercontent.com/assets/11177693/17555973/eb10414c-5f11-11e6-93f9-0ecec4734b50.png)



### 3. Analysis

#### 3. 1. Cluster Analysis

* Back to the main menu of the layers, select the ``customer_home_locations`` layer and click on ``ADD ANALYSIS`` option.

* From the analysis menu, select the ``Calculate cluster points`` analysis and click on ``ADD ANALYSIS``.

![cluster](https://cloud.githubusercontent.com/assets/11177693/17555994/fb2fceb2-5f11-11e6-8581-fac500980730.png)


* In the ``ANALYSES`` tab of the layer, we have three sections:
	* **Workflow**: Is an overview of the analysis that we apply to the layer, so you can have more than one. The analysis should have the name ``A1`` to indicate that is the first analysis applied to the layer.
	* **Source**: asks for the geometry where we will calculate the cluster of the ``customer_home_locations`` layer.
	* **Parameters**: Define the number of possible stores that you want. We set the number of clusters to 6.

![cluster_param](https://cloud.githubusercontent.com/assets/11177693/17556057/458239c8-5f12-11e6-90f4-5fb95316bab3.png)


* After clicking ``Apply``, CARTO will return the result of the ``Calculate cluster points`` analysis. After finishing the analysis, CARTO will return the same number of points on the map, but with an extra column called ``cluster_no``.
* In order to have a better understanding of the result of the ``Calculate cluster points`` analysis and see how the points are grouped, we should change the fill option of the points (in the ``Style`` tab) according to the value of the ``cluster_no`` column using the ``BY VALUE`` option:

![style_cluster](https://cloud.githubusercontent.com/assets/11177693/17556078/61faae5a-5f12-11e6-8a64-3c16236b48f2.png)


#### 3. 2. Weight Centroid Analysis

* We will apply the analysis to the  result of the ``Calculate cluster points``  analysis, so we will go back to the main menu and we will  click on the ``ADD ANALYSIS`` option of the ``customer_home_locations`` layer.
* We will select the ``Find centroid of geometries`` analysis.

![centroid](https://cloud.githubusercontent.com/assets/11177693/17556117/884168f6-5f12-11e6-8313-45c8a0ff577e.png)

* In the ``ANALYSES`` tab of the layer, we have two sections:
	* **Workflow**: Now, because we are applying a second analysis to the ``customer_home_locations`` layer, the workflow has changed. ``A1`` represent the cluster analysis, but now we have a new analysis named ``A2`` to indicate that is the second analysis applied to the layer.
	* **Centroid**:
		* *Source*: we indicate that we are using as the source, the results from the ``Calculate cluster points``  analysis. The source is not the original points of the layer, but the points that we got after the ``Calculate cluster points``  analysis.
	* **Parameters**: to set how we want to calculate the centroids of the cluster data. We will select the  ``Categorized by`` option using the ``cluster_no`` column, we will also select the ``Aggregated by`` parameter to aggregate  the result using the average  of the ``customer_value`` column and finally we will select the ``Weigthted by`` parameter using the ``customer_value`` column to indicate the column that we want to use to calculate the centroids of the clustered regions.

![centroid_param](https://cloud.githubusercontent.com/assets/11177693/17556178/c24bbc4a-5f12-11e6-866d-b70784abdc05.png)

* After clicking ``Apply``, we should see a result where we can see the centroids of the clustered areas from the Cluster Analysis

![centroid_result](https://cloud.githubusercontent.com/assets/11177693/17556251/055b6242-5f13-11e6-80e7-06ff2eee4e19.png)


##### 3. 2. 1. Improve visualization

* We could style our resulting points by changing the size and the color according to the resulting aggregated values.

![centroid_styled](https://cloud.githubusercontent.com/assets/11177693/17556256/0fd68f80-5f13-11e6-835d-e3c62657aafb.png)


* We also could add a popup to the layer, so for each point we can display its value. In order to do this we go to the ``Popup`` tab and we select the ``hover`` option to display the popup when we mouse over the centroids.
We select the column ``value`` to display its values in the popup.  We also will change the name that will be displayed on the popup to ``AVG. CUSTOMER VALUE``.

![popup](https://cloud.githubusercontent.com/assets/11177693/17556317/3b40755a-5f13-11e6-9623-4b4e4bdf4e20.png)

* Back to the main menu, in the ``Layers``tab,we drag and drop the Cluster node analysis outside of the layer (layer A1) to create a new data layer with the customer locations (layer B).By doing this, we will have on the map a layer with the clustered points and a layer with their centroids.

![drag_drop](https://cloud.githubusercontent.com/assets/11177693/17556368/741d3f2a-5f13-11e6-8575-67f47006b9d0.png)

* Now, we change the name of the layers.The name of Layer A will be ``Centroids`` and the name of layer B will be ``Customer Locations``. Then, we change the style of the ``Customer Locations`` layer using the values from the column ``cluster_no`` in order to style the points depending on the cluster they belong to.

![style_2](https://cloud.githubusercontent.com/assets/11177693/17556407/9adcc5f4-5f13-11e6-9a39-4368cb65b2d7.png)

#### 3. 3. Area of influence analysis

* We will apply the analysis to the  result of the Centroid Analysis.We will go back to the main menu and we will  click on the ``ADD ANALYSIS`` option of the ``Centroids`` layer (A).
* We will select the ``Create areas of influence`` analysis.

![aoi](https://cloud.githubusercontent.com/assets/11177693/17556443/b2f22f80-5f13-11e6-8aa4-22cb8bbb0f9f.png)


* In the ``ANALYSES`` tab of the layer, we have three sections:
	* **Workflow**: Now, because we are applying a third analysis to the ``Centroids`` layer, the workflow has changed. ``A1`` represent the cluster analysis, ``A2`` represent the Centroid analysis and ``A3`` indicates the third analysis applied to the layer.
	* **Create areas of influence**:
		* *Input*: we indicate that we are using as the input, the results from the ``Centroid`` analysis. The input is not the original points of the layer, but the points that we got after the ``Centroid`` analysis.
	* **Parameters**: define the distance of the area of influence, the type of units, the radius and the boundaries. The boundaries might be ``intact`` or ``dissolved``. If we choose the ``intact`` option, that means that if our areas of influence polygons overlap, then they will keep their original polygon borders. On the other hand, if we choose the ``dissolve`` option, if the areas of influence polygons overlap, they will be merged so the result will be one big polygon. We set the units to kilometres, set the radius to ``1`` kilometre and choose the ``intact`` option for the ``boundaries`` parameter.
* After clicking ``Apply``, we should see a result where we can see the areas of influence of 100 meters around the subway stations:

![aoi_result](https://cloud.githubusercontent.com/assets/11177693/17556494/e1f8aa52-5f13-11e6-81f4-6b92ef10ecf0.png)


##### 3. 3. 1. Improve visualization

* Back to the main menu, in the ``Layers``tab,we drag and drop the Area of influence node analysis outside of layer (A3) to create a new Data layer with the areas of influence (C). The new layer (C) will have the same name as the layer A, we will change the name of layer C to ``Areas of Influence``.

![areas](https://cloud.githubusercontent.com/assets/11177693/17556500/e7eb561c-5f13-11e6-868e-01537f6e5235.png)



#### 3. 4. Filter Points in polygons analysis

* We will apply the analysis to the ``Areas of Influence`` layer, so we click ``ADD ANALYSIS`` option of that layer and we select the ``Filter points in polygons`` option.


![fpp_options](https://cloud.githubusercontent.com/assets/11177693/17556507/efa15b54-5f13-11e6-9fe7-e5d2be9d3524.png)



* In the ``ANALYSES`` tab of the layer, we have two sections:
	* **Workflow**: Now, because we are applying a second analysis to the ``Areas of Influence`` layer, the workflow has changed. ``B1`` represent the area of influence analysis, but now we have a new analysis named ``B2`` to indicate that is the second analysis applied to the layer.
	* **Filter points in polygons**:
		* *Source*: we indicate that we are using as the source, the results from the ``area of influence`` analysis. The source is not the original points of the layer, but the polygons that we got after the ``area of influence`` analysis.
		* **Target Layer**: is the ``Clusters`` layer (B1).

* After clicking ``Apply``, we should see a result where insead of the circles we had before on our ``Areas of Influence`` Layer, we should have now points refering to customers within those ``Areas of Influence``. The result is indicating the customers that are closer to the stores.

![fpp_result](https://cloud.githubusercontent.com/assets/11177693/17556514/f7857e40-5f13-11e6-94f3-bfcee82ba403.png)

### 4. Widgets

#### 4. 1. Formula widget

* Back to the main menu, select the WIDGETS tab and select the ADD WIDGET option.
* In the options of the Formula tab, select the ``cartodb_id``column of the B1 layer and we click on ``CONTINUE``.

![wid1_option](https://cloud.githubusercontent.com/assets/11177693/17556528/0a042c10-5f14-11e6-9ab3-d5642cf9691c.png)


* In the widget menu, we set the ``OPERATION`` parameter to ``count`` and we change the name of the widget to ``Total Customers``.

![wid1_result](https://cloud.githubusercontent.com/assets/11177693/17556546/125cf658-5f14-11e6-97d5-8ad54bdea71f.png)

* Back to the main menu, select the WIDGETS tab and select the ADD WIDGET option.
* In the options of the Formula tab, select the ``cartodb_id``  of the C2 layer.

![wid2_option](https://cloud.githubusercontent.com/assets/11177693/17556554/18acc538-5f14-11e6-91ca-01b225c5544d.png)


* In the widget menu, we set the ``OPERATION`` parameter to ``count`` and we change the name of the widget to ``Customers within Areas of influence``.

![wid2_result](https://cloud.githubusercontent.com/assets/11177693/17556559/1d41cb20-5f14-11e6-8b1c-c33cae71212a.png)


#### 4. 2. Histogram widget

* Back to the main menu, select the WIDGETS tab and select the ADD WIDGET option.
* In the options of the Histogram tab, select the ``customer_value`` of the B1 layer and we click on ``CONTINUE``.

![wid3_option](https://cloud.githubusercontent.com/assets/11177693/17556566/223d6fbc-5f14-11e6-8ddb-22ba492944a2.png)


* We should get a "U shape" histogram:

![wid3_result](https://cloud.githubusercontent.com/assets/11177693/17556569/26ed2250-5f14-11e6-9189-70cbdc850b9c.png)


* Go back to the main menu, and filter your map with the histogram. Watch how the map changes !

![filter](https://cloud.githubusercontent.com/assets/11177693/17556577/2ba34450-5f14-11e6-9f99-8245ccb065e6.png)
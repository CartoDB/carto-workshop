# Trees of San Francisco

* *Degree of Difficulty*: **

* *Goal*: Display the different kinds of trees in San Francisco and when they were planted.

* *Features Highlighted*:
	* Widgets: Category and time-series widget. 

* *Datasests needed*:
	* sf_trees dataset from the [San Francisco Trees map](https://team.carto.com/u/builder-demo/viz/10be49bc-6683-466e-8cbe-c11d4ef4aa95/public_map)
	

## Instructions

### 1. Import and create map

#### 1. 1. Import the ``Street Tree List`` as a csv file into your dataset dashboard.

* Show how easy is to import files into CARTO! Explain the viewer the wide diversity of geodata supported in CARTO during the importing.

#### 1. 2. Dataset view

* Don't waste time with this. But show the `METADATA`-`SQL` switch and the `PREVIEW`.

#### 1. 3. Click on ``CREATE MAP`` from the ``Street Tree List`` dataset. Change the name of the map to ``San Francisco Trees``

> **Warning**: after renaming a layer a error could pop up saying "the map cannot be rendered", don't worry about this. Refresh the page and it will dissapear.



### 2. Layers and styles

#### 2. 1. Show the new ordering of the layers in the Builder

* Change the basemap to the dark_matter (labels below) basemap to show the different basemaps options.

* Explain that layers have a dataset layer source. For instance, the source of the ``sf_trees`` layer is the dataset **``sf_trees``**, aka ``a0``. This would be very important when adding analysis and widgets. 

#### 2. 2. Show the different options layers have

> Comment that each layer has 5 options: `DATA`, `ANALYSES`, `STYLE`, `POP-UPS` and `LEGENDS`.

* `DATA`: 
  * Explain that this interface gives a general view of the fields the layer, its name and its data type but also from there you can add them as widgets. 
  * Switch to `VALUES` to `SQL`. Explain that the SQL command line allows more advanced users to manage data in a more precise way.
  * Finally, show how easy is to change from map view to dataset view.

* `STYLE`:
  * In the ``Aggregation menu``, select the ``None`` option.
  * In the ``Fill`` options, select the ``By Valye`` option and then choose the ``common_species`` option to color the dots depending on the values of the ``common_species`` values.
  * Change the size of the dots to 3 and change the stroke value of the points to 0.

![style](https://cloud.githubusercontent.com/assets/11177693/17486997/4718a482-5d93-11e6-838f-0865c1b57385.png)


* `POP-UPS`:
  * Select the Hover tab and the light style for the hover infowindow.
  * In the ``Show Items`` section of the hover menu, select the ``common_species`` field and change its name to Common Species.
  * By doing this, a pop-up will appear when we hover over the points on the map.

![hover](https://cloud.githubusercontent.com/assets/11177693/17486993/420e9e24-5d93-11e6-8a94-7b9dd9b90aa2.png)



### 3. Widgets

#### 3. 1. Category Widget

* Back to the main menu, select the ``WIDGETS`` tab and select the ``ADD WIDGET``. 
* In the options of the ``Category`` tab, select the ``common_species`` and ``caretaker`` checkboxes in order to have widgets that display the different categories of the ``common_species`` and ``caretaker`` fields. Click ``CONTINUE``to add the widgets on the map.

![category](https://cloud.githubusercontent.com/assets/11177693/17486990/3d021a32-5d93-11e6-9ad0-7bae2f8d787a.png)


#### 3. 2. Time series widget

* Back to the main menu, select the ``WIDGETS`` tab and select the ``ADD WIDGET``. 
* In the options of the ``Time-series`` tab, select the ``plant_date`` checkbox in order to display the dates were the trees were planted. Click ``CONTINUE``to add the widgets on the map.

![time](https://cloud.githubusercontent.com/assets/11177693/17486985/385eb94a-5d93-11e6-8b00-6ab790c97d5f.png)


#### 3. 3. Change order and name of widgets

* Back to the main menu, select the ``WIDGETS`` tab to see a list with the widgets that we have added. 
* We can drag and drop the widgets in the widget menu to change their order.

![widgets](https://cloud.githubusercontent.com/assets/11177693/17486975/33b7c92c-5d93-11e6-8037-c25cc3272bc0.png)


* We can change the name of our widgets by selecting the ``rename`` option of the widget menu or by doing a double click on the widget name in the widgets menu. 

![rename](https://cloud.githubusercontent.com/assets/11177693/17486968/2f02e06a-5d93-11e6-931c-e8e237a1969e.png)


* Change the name of the ``common_species`` widget to ``Common Species``, the name of the ``caretaker`` widget to ``Caretaker`` and the name of the ``plant_date`` widget to ``Plant Date``.

![names_changed](https://cloud.githubusercontent.com/assets/11177693/17486963/2ad66318-5d93-11e6-824b-f6ad8e1875a8.png)



### 4. Share map

* At the bottom of the main menu, click the ``Share`` button.
* Select the ``Publish`` tab and click on the ``Publish`` button that is below the Map title in order to share our map.

![publish](https://cloud.githubusercontent.com/assets/11177693/17486954/2471f492-5d93-11e6-8ca9-6be7d27d0a2c.png)


* After clicking the ``Publish`` button, we can select the option that we want to share our map.

![share](https://cloud.githubusercontent.com/assets/11177693/17486948/1d60b120-5d93-11e6-9d74-af8ca56667c9.png)

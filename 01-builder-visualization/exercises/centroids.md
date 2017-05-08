# How to show two symbologies in the same layer

* *Degree of Difficulty*: **
* *Goal*: How to display two different symbologies in the same layer
* *Features Highlighted*:
  * Analysis: `Find centroids of geometries`
  * Layers: how to work with layer nodes
  * Style: choropleth and bubble maps
* *Datasests needed*:
  * `world_commerce` dataset: world country data from 2015 about economy and commerce.

1. [Import and create map](#map) <br>
2. [Get centroids](#centroids) <br>
3. [Style](#style) <br>
4. [Public and share map](#public) <br>

<hr>

## 1. Import and create map <a name="map"></a> 

### 1. 1. Import `world_commerce` file into your DATASETS dashboard.

* Download the dataset from [here](https://builder-demo.carto.com/api/v2/sql?q=SELECT+*+FROM+world_commerce&format=geojson&filename=world_commerce)
* Go to your account and import it using the **NEW DATASET** options
* Select **CONNECT DATASET**
* Drag and drop the file

### 1. 2. Dataset view 

* Take a look on the dataset
* Switch between the metadata and the SQL view, try any simple query like limiting the result.

### 1. 3. Click on **CREATE MAP**

* Change the name of the map to **World Economic and Commerce Data (2015)** (double click!)
* Change the name of the layer to **World Countries**

<hr>

## 2. Get centroids <a name="centroids"></a> 

### 2. 1. `Find centroids of geometries` analysis



![centroids](imgs/01-centroids-01.png)

### 2. 2. Recover the original layer



![node](imgs/01-node-02.png)

<hr>

## 3. Style <a name="style"></a> 

### 3. 1. Style centroids as bubbles




### 3. 2. Style polygons as thematic choropleth



![style](imgs/01-centroids-03.png)

<hr>

## 4. Public and share map <a name="public"></a> 

* At the bottom of the main menu, click on **SHARE** at the right button corner.
* Go to **PUBLISH** tab and click on **PUBLISH** button that is below the Map name in order to share our map.
* After clicking on **PUBLISH**, we can select the options that we want to share our map.
  * Get the link URL and paste it on other browser tab: 

![map](imgs/01-centroids-04.png)

<hr>

# Valencia Bikes

* *Degree of Difficulty*: **
* *Goal*: Find the nearest AirBnB listings to playgrounds in San Francisco
* *Features Highlighted*:
  * Analysis
* *Datasests needed*
  - San Francisco AirBnBs: download it from the [builder-demo]() CARTO account
  - San Francisco Playgrounds: download it from the [builder-demo]() CARTO account

## Contents

<!-- MarkdownTOC -->

- Import Datasets and Create Map
- Analysis 1: Apply ```find nearest analysis``` to find closest AirBnB listings to San Francisco playgrounds
- Analysis 2: Apply ```connect with lines``` analysis to draw a line from each playground to the closest AirBnB listings
- Widgets
- Map options and publish

<!-- /MarkdownTOC -->

## 1. Import datasets and create a map

### 1. 1. Import datasets into CARTO

* It's easy to import files into CARTO! Drag and drop the `san_francisco_airbnbs` csv file in your CARTO Datasets dashboard first, then the `san_francisco_playgrounds` csv. 

* Select `san_francisco_airbnbs` and `san_francisco_playgrounds`, click on `Create map`.

### 1. 2. Rename Map Title and Layers

* Rename map title to `SF Find Nearest Demo`.
* Rename layers:
  1. `san_francisco_airbnbs` as `airbnbs`,
  2. `san_francisco_playgrounds` as `playgrounds`,
```

### 1. 3. Change the Basemap

* Click on the ```Positron Basemap``` layer in the layer panel 
* Select the ```Dark Matter (Labels Below)``` basemap
* Click on the back arrow button in the upper left hand side of the ```Basemap``` panel to navigate back to the layer panel
```

## 2. Analysis 1: Apply ```find nearest analysis``` to find closest AirBnB listings to San Francisco playgrounds

### 2. 1. Apply ```find nearest``` analysis to ```playgrounds``` layer

* Click on the ```playgrounds``` layer in the layer panel
* Click on the ```ANALYSIS``` tab and ```ADD ANALYSIS```
* Select ```Find nearest``` analysis
* Click on ```ADD ANALYSIS```
* Set the parameters as follows:
  * `TARGET`: `A0 Source airbnbs`.
  * `MAX RESULTS`: `5`
* Click on ```APPLY```

### 2. 2. Style Your Map

* After applying the analysis, drag and drop out the original node layer. Rename the analysis node layer as `find nearest`.

> Now you should have a mess of points, three layers equally styled.

* Begin by styling the ```airbnbs``` layer:
  * Click on `airbnbs` layer.
  * In the ```STYLE``` tab:
    * `FILL`: set the marker size value to `7` and color to #bcb5aa (`#bcb5aa`).
    * `STROKE`: set stroke width to `0`.
    * Use the zoom control panel in the lower left of the map to zoom in to zoom 14

* Style the ```playgrounds``` layer next:
  * Click on `playgrounds` layer.
  * In the ```STYLE``` tab:
    * `FILL`: click on the color bar and choose `IMG`, select `Show full collections`, choose the seesaw Maki icon, click on `SET IMAGE`
    * `FILL`: set the marker icon size to `22`
    * `STROKE`: set stroke width to `0`.


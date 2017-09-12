# How to create a Game of Thrones basemap

* Goal: create a basemap based on Game of Thrones world.
* Based on [*Our Game of Thrones Basemap is here to unite the Seven Kingdoms* blogpost](https://carto.com/blog/game-of-thrones-basemap/).

## Guide

0. [Resources](#resources)
1. [Getting started](#getting-started)
2. [Ocean and ripples](#ocean)
3. [Continents and islands](#continents)
4. [Lakes and mountains](#lakes)
5. [Rivers and roads](#rivers)
6. [The Ice Wall and beyond](#wall)
7. [Regions and towns](#locations)
8. [You know nothing!](#basemap)

### 0. Resources <a name="resources"></a>

* [Template .carto file](https://drive.google.com/file/d/0B9k_lcYQZACgOVJsRlQ3ZUZQeGM/view?usp=sharing)
* [Final .carto file](https://drive.google.com/file/d/0B9k_lcYQZACgdzVwRXhkRGpSQ3M/view?usp=sharing)
<br>
*All thematic data was built from the gvSIG’s ebook, [*Learning GIS with Game of Thrones*](http://downloads.gvsig.org/download/documents/books/GoT_book_GIS_gvSIG.pdf).

### 1. Getting started <a name="getting-started"></a>

* Import the template .carto file into your account.
* Open the map.
* Rename your map as *Game of Thrones Basemap*.
* Make sure that the order and names of your layers are as follows:<br>
  1. `locations` > *Locations*
  2. `wall` > *The Ice Wall*
  3. `political` > *Regions*
  4. `rivers_and_roads` > *Rivers and roads*
  5. `mountains_lakes` > *Mountains and lakes*
  6. `political` > *Beyond the Wall*
  7. `continents_and_islands` > *Continents and islands*
  8. `continents_and_islands` > *Ripples*

![getting-started](img/got-getting-started.png)

### 2. Ocean and ripples <a name="ocean"></a>

* First, change the basemap:
  1. Click on the *Positron* basemap
  2. Select `COLOR`
  3. Replace the default color with `#98b4ae`

![ocean](img/got-ocean.png)

* In order to create a coastal ripple effect, follow these instructions:
  1. Click on the *Ripples* layer and go to the ANALYSIS tab
  2. Click on ADD ANALYSIS button to add a new analysis 
  2. Select *Create Areas of influence*
  3. Click on ADD ANALYSIS
  4. Set parameters as folows:
    * *TYPE*: `Distance`
    * *UNITS*:  `mi` (miles)
    * *RADIUS*: `60`
    * *TRACTS*: `4`
    * *BOUNDARIES*: *Dissolved*
  5. Hit on APPLY
  6. Go to the STYLE tab and switch from VALUES to CARTOCSS
  7. Replace the default style with the following one:

  ```css
  #layer {
    line-width: 1;
    line-color: #FFF;
    line-opacity: 0.2;
    line-comp-op: soft-light;
  }
  ```
  8. Hit on APPLY

![ripples](img/got-ripples.png)

### 3. Continents and islands <a name="continents"></a>

### 4. Lakes and mountains <a name="lakes"></a>

### 5. Rivers and roads <a name="rivers"></a>

### 6. The Ice Wall and beyond <a name="wall"></a>

### 7. Regions and towns <a name="locations"></a>

### 8. You know nothing! <a name="basemap"></a>

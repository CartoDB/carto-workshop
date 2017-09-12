# How to create a Game of Thrones basemap

* Goal: create a basemap based on Game of Thrones world.
* Based on [*Our Game of Thrones Basemap is here to unite the Seven Kingdoms* blogpost](https://carto.com/blog/game-of-thrones-basemap/).

## Guide

0. [Resources](#resources)
1. [Getting started](#getting-started)
2. [Ocean and ripples](#ocean)
3. [Continents and islands](#continents)
4. [Mountains and lakes](#mountains)
5. [Rivers and roads](#rivers)
6. [The Ice Wall and beyond](#wall)
7. [Regions and towns](#locations)
8. [You know nothing!](#basemap)

### 0. Resources <a name="resources"></a>

* [Template .carto file](https://drive.google.com/file/d/0B9k_lcYQZACgOVJsRlQ3ZUZQeGM/view?usp=sharing)
* [Final .carto file](https://drive.google.com/file/d/0B9k_lcYQZACgMHU1aXlmdW9ac00/view?usp=sharing)

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

* Disable the view from all your layers but *Continents and islands*
* In order to style the land, follow these steps:
  1. Click on *Continents and islands* layer
  2. Switch from VALUES to CARTOCSS
  3. Replace the default style with the following one:


  ```css
  #layer {
  polygon-pattern-file:
  url('https://s3.amazonaws.com/com.cartodb.users-assets.production/production/mamataakella/assets/20170822202613TexturesCom_PaperDecorative0061_1_seamless_S.jpg');

    polygon-pattern-opacity: 0.4;
    polygon-fill:mix(#6ea92f,#CCBE9A,40);
    polygon-opacity: 0.8;
    polygon-comp-op: multiply;
    line-width: 5;
    line-color: fadeout(#fff,85);
    
    [zoom<=4]{line-width: 2.5;}
  }
  ```

  4. Hit on APPLY

![continents](img/got-continents.png)

### 4. Mountains and lakes <a name="mountains"></a>

* Enable the view from the *Mountains and lakes* layer
* Again, follow these instructions to style this layer:
  1. Click on *Continents and islands* layer
  2. Switch from VALUES to CARTOCSS
  3. Replace the default style with the following one:

  ```css
  #layer [type = 'mountain']{
   polygon-pattern-file:
  url('https://s3.amazonaws.com/com.cartodb.users-assets.production/production/mamataakella/assets/20170823212836mountain-range.png');
    polygon-pattern-opacity: 0.3;

  }
  #layer[type='lake'] {
    polygon-fill: #718c9f;
    polygon-opacity: 0.7;
    line-width: 1;
    line-color: rgba(255,255,255,0.1);
  }

  #layer[type='swamp']{
    polygon-opacity: 0.4;
    polygon-fill: mix(#6ea92f,#718c9f,30);
  }

  ```

  4. Hit on APPLY

![mountains](img/got-mountains.png)

### 5. Rivers and roads <a name="rivers"></a>

### 6. The Ice Wall and beyond <a name="wall"></a>

### 7. Regions and towns <a name="locations"></a>

### 8. You know nothing! <a name="basemap"></a>

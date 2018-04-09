# Customize Builder Styles for More Meaningful Maps

### Index

1. [Introduction](#intro)

2. [Which basemap to use?](#basemap)

3. [How to separate signal from noise](#lines)<br>

4. [Communicate Effectively](#communicate)<br>


  ​
### 1. Introduction <a name="intro"></a>

This workshop will demonstrate how Builder map default cartography can be adjusted to be more meaningful with a few customizations.

CARTO Builder dashboards are a holistic experience, so their elements should be balanced visually. We will cover examples of how to do that, and topics like assiging widget names, custom legends, and popups...all with the goal of not using Builder defaults as-is and ideas for customization.



### 2. Which basemap to use?<a name="basemap"></a>

[SF Crime Heatmap](https://cartoworkshops.carto.com/u/carto-workshops/builder/3d71c79c-2616-4974-8e61-7cb10c3c0f6f/embed)

#### Which basemap should you use? 
* Consider labels on top or under geometry
* Consider Voyager, Positron, Dark Matter

#### Consider symbology + basemap
* How can you make symbology emphasize the data's insights?

*Default:*

![heat-default](img/c_heat_default.png)

* Basemap labels can cover point data.
* Default settings = one big "blob" of heat. Adjust size, duration, steps & resolution to make the data legible.
* Since this is crime, think of changing the color scheme to something that shows the high in red and lows in a cooler color like green.
  * [CARTOColors](https://carto.com/carto-colors/) `Temps` divergent scheme
* Grays of Positron basemap are closer to low crime/green heat, so high crime/red heat is emphasized even more.
* Don't forget you can customize the Legend!

*Adjusted:*

![heat-adjusted](img/c_heat_adjusted.png)



### 3. How to separate signal from noise<a name="lines"></a>

[NY Commuter Flow Map](https://cartoworkshops.carto.com/u/carto-workshops/builder/1b59856b-e4d4-4bdc-abe6-d993e1eb5b89/embed)

#### How can you pick out information from this many lines?
* Use classed line symbology
* Use a CARTOColor scheme
* Use redundant symbology
* [Again] Consider the symbology + basemap

*Default:*

![flow-default](img/c_lines_default.png)

* All lines look equal by default
* 

*Adjusted:*

![flow-adjusted](img/c_lines_adjusted.png)



### 4. Leverage Autostyling<a name="autostyle"></a>



### 5. Communicate Effectively<a name="communicate"></a>


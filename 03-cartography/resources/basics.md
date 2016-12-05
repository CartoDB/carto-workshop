# CartoCSS basics

## Category

In the following block of code polygon opacity is applied to the overall style of the map. While `polygon-fill` is removed from the overall map style, since the default values for these properties do not render any styling effects, they are not necessary.

*Tip*: In some cases, default values for CartoCSS properties render no styling effects on your map. If you apply CartoCSS syntax with the default values `none``undefined`, the map appears the same with or without these properties. Ensure to define values for properties that apply no default styling.

Each point is categorized as `[continent="name"] {` and contains its own marker-fill style. You do not need to preface each point with the `#layer` label. Note how syntax highlighting is applied to clearly indicate the category.

```css
#layer {
  /* global */
  polygon-opacity: 0.9;

  /* categories */
  [continent="Africa"] {
    polygon-fill: #A6CEE3;
  }
  [continent="Antarctica"] {
    polygon-fill: #1F78B4;
  }
  [continent="Asia"] {
    polygon-fill: #B2DF8A;
  }
  [continent="Australia"] {
    polygon-fill: #33A02C;
  }
  [continent="Europe"] {
    polygon-fill: #FB9A99;
  }
  [continent="North America"] {
    polygon-fill: #E31A1C;
  }
  [continent="Oceania"] {
    polygon-fill: #FDBF6F;
  }
  [continent="South America"] {
    polygon-fill: #FF7F00;
  }
}
```

<br>
![category](https://github.com/CartoDB/cdmx-training/blob/master/03-cartography/exercises/img/category.png)
<br>

The following two styles are based on the same principle, but instead of using string fields, choropleth and bubbles styles use numeric ones.

## Choropleth

```css
#layer {
  /* global */
  polygon-opacity: 0.7;
  line-width: 1;
  line-color: #FFF;
  line-opacity: 0.5;
  polygon-fill: #fcde9c; /* polygon-fill less than or equal to 3178328.56452752 */

  /* categories */
  [ pop_norm > 169360.836697335 ] {
    polygon-fill: #f58670;
  }
  [ pop_norm > 540892.19330855 ] {
    polygon-fill: #e34f6f;
  }
  [ pop_norm > 952277.445247449 ] {
    polygon-fill: #d72d7c;
  }
  [ pop_norm > 3178328.56452752 ] {
    polygon-fill: #7c1d6f;
  }

}
```

<br>
![choropleth](https://github.com/CartoDB/cdmx-training/blob/master/03-cartography/exercises/img/choropleth.png)
<br>

## Bubbles

```css
#layer {
  /* global */
  marker-fill: #af5cda;
  marker-fill-opacity: 0.5;
  marker-allow-overlap: true;
  marker-line-width: 0.2;
  marker-line-color: #FFF;
  marker-line-opacity: 1;
  marker-width: 1; /* marker-width less than or equal to 2.5 */

  /* categories */
  [ pop_max > 9461 ] {
    marker-width: 2.5;
  }
  [ pop_max > 27200 ] {
    marker-width: 4;
  }
  [ pop_max > 53800 ] {
    marker-width: 5.5;
  }
  [ pop_max > 98852 ] {
    marker-width: 7;
  }
  [ pop_max > 187765 ] {
    marker-width: 8.5;
  }
  [ pop_max > 440006 ] {
    marker-width: 10;
  }
}
```

<br>
![bubbles](https://github.com/CartoDB/cdmx-training/blob/master/03-cartography/exercises/img/bubbles.png)
<br>

## Torque

## Zoom-Based Styling

The first tool we will look at is zoom-based styling. Zoom-based styling refers to the ability to change what is displayed on a map, or how it is visualized, based on the zoom-level. Let’s start by looking at [Stamen’s map tiles](http://maps.stamen.com/#terrain/12/37.7706/-122.3782). As you zoom in and out, you can notice that some features or data (like streets, buildings, or waterways) appear or fade away. While there is a ton of data in the map, it is simplified when you’re zoomed out, and made more complex at closer scales, when a viewer is able to process more data. The map never becomes overly complex, but also manages to provide a very data-rich view of a city.

<br>
![stamen](https://carto.com/academy/img/course2/lesson3/stamen.2c358963.gif)
<br>

Before we start making changes based on our zoom level, it’s important to note that online maps using [Mapnik](http://mapnik.org/) to build the map visualization will default to having marker widths stay the same, regardless of the level of zoom. In order to style your maps based on zoom level in these online maps (including CARTO, OpenStreetMap and MapBox), we’ll be using CartoCSS, which we started learning about in our last lesson.

To start working with zoom-based styling, let’s go back to the Simple visualization, and reduce the marker size to around 3 so that we can see more of our data points. In the CartoCSS window, we’ll add some new styling so that at different zooms, the size of the marker gets bigger. Here, we want the markers to get bigger the more zoomed in we are. We want to tell CARTO that if the zoom is equal to a certain level, the marker-width should be larger than the original 3. We could also tell CARTO to change marker width at all zoom levels larger than a specified level. Take a look at the last three lines of our code block here.

```css
#layer{
  marker-fill-opacity: 0.9;
  marker-line-color: #FFF;
  marker-line-width: 0;
  marker-line-opacity: 1;
  marker-placement: point;
  marker-type: ellipse;
  marker-width: 3;
  marker-fill: #FF6600;
  marker-allow-overlap: true;
  [zoom = 4] {marker-width: 6}
  [zoom = 5] {marker-width: 12}
  [zoom > 5] {marker-width: 16}
}
```

We can see that CARTO will read this as all markers should have a width value of 3. If the zoom equals 4, the marker width value should be 6. If the zoom equals 5, the marker width value should be 12. Finally, if the zoom is larger than 5, the marker width value should be 16. This means that as we zoom in, the markers become bigger. [Go ahead and play around with this](https://team.carto.com/u/ramirocartodb/builder/33e2696c-badf-11e6-80bd-0ee66e2c9693/embed) to see what kinds of visualizations you can make based on zoom.

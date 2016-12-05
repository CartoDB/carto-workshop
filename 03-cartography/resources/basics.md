# CartoCSS basics

## Category

In the following block of code polygon opacity is applied to the overall style of the map. While `polygon-fill` is removed from the overall map style, since the default values for these properties do not render any styling effects, they are not necessary.

*Tip*: In some cases, default values for CartoCSS properties render no styling effects on your map. If you apply CartoCSS syntax with the default values `none``undefined`, the map appears the same with or without these properties. Ensure to define values for properties that apply no default styling.

Each point is categorized as `[continent="name"] {` and contains its own polygon-fill style. You do not need to preface each point with the `#layer` label.

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



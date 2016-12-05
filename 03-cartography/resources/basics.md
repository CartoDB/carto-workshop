# CartoCSS basics

## Category

In the following block of code polygon opacity is applied to the overall style of the map. While `polygon-fill` is removed from the overall map style, since the default values for these properties do not render any styling effects, they are not necessary.

*Tip*: In some cases, default values for CartoCSS properties render no styling effects on your map. If you apply CartoCSS syntax with the default values `none``undefined`, the map appears the same with or without these properties. Ensure to define values for properties that apply no default styling.

Each point is categorized as `[continent="name"] {` and contains its own marker-fill style. You do not need to preface each point with the `#layer` label. Note how syntax highlighting is applied to clearly indicate the category.

```css
#layer {
  polygon-opacity: 0.9;

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

## Choropleth

## Bubbles

## Torque

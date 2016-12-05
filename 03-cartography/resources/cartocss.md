# CartoCSS

CartoCSS is the syntax language that enables you to customize the style of your map data. Similar to working with a [Cascading Style Sheet (or CSS) for styling webpages](https://en.wikipedia.org/wiki/Cascading_Style_Sheets), **CartoCSS is specific to the design of map symbolizer properties** (such as *marker size*, *marker color*, *line pattern*, *line stroke*, *text display*, and so on). You can apply CartoCSS code directly from the STYLE options of a selected map layer. Optionally, you can apply CartoCSS with [CARTO.js](https://carto.com/docs/carto-engine/carto-js/) and the [Maps API](https://carto.com/docs/carto-engine/maps-api/).

## CartoCSS Properties

Each CartoCSS map category has its own configurable properties. You can apply multiple symbolizers and properties to the same map layer. [In CARTO Documentation](https://carto.com/docs/carto-engine/cartocss/properties/) you can navigate to a selected symbolizer to view a description of the CartoCSS property, sample CartoCSS code, the default value, and the available values.


## CartoCSS Syntax

Note how the CartoCSS syntax is structured and the different CartoCSS properties used when styling the three types of geometries.

### Points

```css
#layer {
  marker-width: 7;
  marker-fill: #FFB927;
  marker-fill-opacity: 0.9;
  marker-line-color: #FFF;
  marker-line-width: 1;
  marker-line-opacity: 1;
  marker-placement: point;
  marker-type: ellipse;
  marker-allow-overlap: true;
}
```
Units for sizes are in pixels unless stated differenetly. Check the documentation for the valid values for properties like [`marker-placement`](https://carto.com/docs/carto-engine/cartocss/properties/#marker-placement-keyword) or [`marker-type`](https://carto.com/docs/carto-engine/cartocss/properties/#marker-type-keyword).

### Lines

```css
#layer {
  line-color: #3EBCAE;
  line-width: 1.5;
  line-opacity: 1;
}
```

### Polygons

```css
#layer {
  polygon-fill: #374C70;
  polygon-opacity: 0.9;
  polygon-gamma: 0.5;
  line-color: #FFF;
  line-width: 1;
  line-opacity: 0.5;
  line-comp-op: soft-light;
}
```

<br>
![style](https://github.com/CartoDB/cdmx-training/blob/master/03-cartography/exercises/img/style.png)
<br>

## CartoCSS variables

Apply the `@` symbol to lists of all the color values for your categories. The CartoCSS syntax is structured so that you can apply all your color changes in one section `@name: color;` and reference the point style within the category label `marker-fill: @name;`. This enables you to visualize exactly where your marker-fill values are located, in addition to the overall map styles*.

```css
@fill: #A64942;
@line: #FF7844;

#layer {
  marker-width: 5;
  marker-fill: @fill;
  marker-fill-opacity: 0.9;
  marker-line-color: @line;
  marker-line-width: 1;
  marker-line-opacity: 1;
  marker-placement: point;
  marker-type: ellipse;
  marker-allow-overlap: true;
}
```

<br>
![variables](https://github.com/CartoDB/cdmx-training/blob/master/03-cartography/exercises/img/variables.png)
<br>

*Note*: using CartoCSS variables inside Turbo CARTO ramp funcions is not supported at the moment.


## CartoCSS Comments

If you are just learning CartoCSS, it might be useful to add comments next to lines of your CartoCSS code. For example, you can add comments to describe a specific hex color, the default value for a property, the available values, and so on.

Enter comments by using the following format in your CartoCSS code. Note the required spacing:

```css
cartocss-property; /* comment */
```

In the following example, there are user comments entered in the marker-line-color, marker-placement, marker-width, and marker-fill CartoCSS properties.

```css
/** simple visualization */

#layer{
  marker-fill-opacity: 0.9;
  marker-line-color: #FFF; /* white */
  marker-line-width: 1;
  marker-line-opacity: 1;
  marker-placement: point; /* options are point, line, interior */
  marker-type: ellipse;
  marker-width: 15; /* default value was 10 */
  marker-fill: #2E5387; /* hex color is St Tropaz */
  marker-allow-overlap: true;
  marker-comp-op: overlay;
}
```

<br>
![comments](https://github.com/CartoDB/cdmx-training/blob/master/03-cartography/exercises/img/comments.png)
<br>


## Multiple Symbolizers for a Map Layer

In some cases, you may need to apply multiple symbolizers to one map layer. For example, a point layer typically contains marker syntax. You can also attach [other compatible symbolizer](https://carto.com/docs/carto-engine/cartocss/properties/#cartocss-symbolizer) properties, to achieve a desired styling effect.

Enter a double-colon symbol `::` to indicate a duplicate map layer without actually adding a new layer to your map. This dummy layer created through CartoCSS styling acts as an attachment, enabling you to apply multiple symbolizers to the selected layer.

Suppose you have a point symbol and want to put a glowing halo around it. You need CartoCSS values for the point style and CartoCSS values for the glowing halo.


```css
/* bottom layer of symbol */
#layer::halo {
  ::halo {
    marker-width: 20;
    marker-fill: teal;
    marker-fill-opacity: 1;
    marker-line-color: #FFF;
    marker-line-width: 0;
    marker-line-opacity: 1;
    marker-placement: point;
    marker-type: ellipse;
    marker-allow-overlap: true;
  }
/* top layer of symbol */
#layer{
   marker-width: 10;
   marker-fill: #FFB927 ;
   marker-fill-opacity: 0.9;
   marker-line-color: #FFF;
   marker-line-width: 0;
   marker-line-opacity: 1;
   marker-placement: point;
   marker-type: ellipse;
   marker-allow-overlap: true;
}
```

* The ::halo describes the style elements that you are applying to the halo
* The default, top layer describes the style elements that you are applying to the point

<br>
![multiple](https://github.com/CartoDB/cdmx-training/blob/master/03-cartography/exercises/img/multiple.png)
<br>

*Note*: Similar to how map layers are rendered, symbolizers are rendered from bottom to top. To see an example, view this live map which is using [multiple symbolizers](https://mamataakella.carto.com/builder/36cb22c8-3334-11e6-ad49-0ecfd53eb7d3/embed) applied to point styles.


## CartoCSS Best Practices

While there are many ways to apply the same visual effects with CartoCSS properties, this section describes the most efficient and intuitive methods for structuring your CartoCSS syntax.

You can apply CartoCSS properties to the overall map style, or to specific map symbolizers (such as markers and points). Sometimes, applying properties to a symbolizer is not the most effective workflow for enhancing your overall map style. Other times, applying a style to the overall map is not rendered if there is no default value defined, and thus, not needed. For example, see how [composite operations](https://carto.com/docs/carto-engine/cartocss/composite-operations/#composite-operation-effects) can be used for color blending, based on style or symbolizer.

When applying CartoCSS syntax, it helps to understand how values are applied to your map:

* The source is where the style is applied (either as a value or as a symbolizer property)
* The destination is the effect on the rest of the map, underneath the source
* Any layers that appear above the source are unaffected by the applied style and are rendered normally
* Typically, you apply CartoCSS properties to different layers on a map. You can add multiple styles and values for each layer
* Alternatively, you can apply CartoCSS by nesting categories and values. Categories contain multiple values listed under the same, single category using brackets `{ }`. This enables you visualize all of the styling elements applied to the overall map or to individual symbolizers, and avoid adding any redundant or unnecessary parameters. This is the suggested method if you are applying styles to a multi-scale map.

*Note*: Be mindful when applying styles to a map with multiple layers. Instead of applying an overall style to each map layer, apply the style to one layer on the map using this nested structure. For example, suppose you have a map with four layers, you can define zoom dependent styling as a nested value in one map layer. You do not have to go through each layer of the map to apply a zoom style. Using the nested structure allows you to apply all of the styling inside the brackets `{ }`. This is a more efficient method of applying overall map styling.
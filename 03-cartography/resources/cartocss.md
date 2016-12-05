# CartoCSS

CartoCSS is the syntax language that enables you to customize the style of your map data. Similar to working with a [Cascading Style Sheet (or CSS) for styling webpages](https://en.wikipedia.org/wiki/Cascading_Style_Sheets), **CartoCSS is specific to the design of map symbolizer properties** (such as *marker size*, *marker color*, *line pattern*, *line stroke*, *text display*, and so on). You can apply CartoCSS code directly from the STYLE options of a selected map layer. Optionally, you can apply CartoCSS with [CARTO.js](https://carto.com/docs/carto-engine/carto-js/) and the [Maps API](https://carto.com/docs/carto-engine/maps-api/).

## CartoCSS Properties

Each CartoCSS map category has its own configurable properties. You can apply multiple symbolizers and properties to the same map layer. [In CARTO Documentation](https://carto.com/docs/carto-engine/cartocss/properties/) you can navigate to a selected symbolizer to view a description of the CartoCSS property, sample CartoCSS code, the default value, and the available values.

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


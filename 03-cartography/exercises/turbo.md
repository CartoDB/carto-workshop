# TURBO VIEWER WORKSHOP

CARTO [BUILDER](https://carto.com/builder/) and [CARTO.js](https://carto.com/developers/carto-js/) use [CartoCSS](https://carto.com/developers/styling/cartocss/) for styling your layers. CartoCSS is very similar in structure to web [CSS standard](https://www.w3.org/Style/CSS/) (Cascading Style Sheets). The code contains a selector that specifies the HTML elements you’re applying style to, a property of that element that you’re going to change the style of, and a value that defines what you want the property to look like.

On top of CartoCSS, CARTO built a preprocessor called [TurboCARTO](https://github.com/CartoDB/turbo-carto) which allows to generate ramps of colors, widths, opacity and other styling properties. In this workshop, we will apply the most common TurboCARTO ramps and check the CartoCSS code working behind the scenes.


## INDEX

* [CARTOCSS vs. TURBO CARTO](#turbo)
  * [Bubbles](#bubbles)
  * [Categories](#categories)
  * [Choropleth](#choropleth)
  * [Color Brewer](#brewer)
  * [Bivariate](#bivariate)
  * [Building](#buildings)
  * [Opacity](#opacity)
  * [Lines](#lines)
  * [Cartocolors](#cartocolors)
* [RESOURCES](#resources)

<hr>

<a name="turbo"></a>
### CARTOCSS vs. TURBO CARTO 

In order to see the difference between CartoCSS and TurboCARTO syntax, we are going to use an ENGINE application. On the one hand, we will be able to see at the same time the TurboCARTO ramps on the CartoCSS text box, and on the other, the CartoCSS translation will appear on the console as conditional blocks.

1. Open the [CARTO basic viewer](http://bit.ly/carto-viewer).
2. Enter this URL `http://ramirocartodb.cartodb.com/api/v1/map` as "Maps API endpoint".
3. Leave empty the "API key".
4. Open the developer tool.

<br>

<a name="bubbles"></a>
#### Bubbles

* Apply the following SQL query:

```sql
SELECT * FROM populated_places
```

* Apply the following CartoCSS style (`CTR + S`):

```css
#layer {
  marker-width: ramp([pop_max], range(1, 10), quantiles(3));
  marker-fill: #af5cda;
  marker-fill-opacity: 0.5;
  marker-allow-overlap: true;
  marker-line-width: 0.2;
  marker-line-color: #FFF;
  marker-line-opacity: 1;
}
```

![bubbles](https://cloud.githubusercontent.com/assets/5215798/19595744/ae6a8740-978b-11e6-9b62-9a467f60ef8c.png)

<br>

<a name="categories"></a>
#### Categories

* Apply the following SQL query:

```sql
SELECT * FROM continents
```

* Apply the following CartoCSS style (`CTR + S`):

```css
#layer {
  polygon-fill: ramp([name], (#5B3F95, #1D6996, #129C63, #73AF48, #EDAD08, #E17C05), category(6));
  polygon-opacity: 0.7;
  line-width: 0.2;
  line-color: #FFF;
  line-opacity: 0.5;
}
```

![categories](https://cloud.githubusercontent.com/assets/5215798/19595751/b2fe7c76-978b-11e6-8e4f-08af1139c450.png)

<br>

<a name="choropleth"></a>
#### Choropleth

* Apply the following SQL query:

```sql
SELECT * FROM world_borders
```

* Apply the following CartoCSS style (`CTR + S`):

```css
#layer {
  polygon-fill: ramp([pop_norm], (#fcde9c, #f58670, #e34f6f, #d72d7c, #7c1d6f), quantiles);
  polygon-opacity: 0.7;
  line-width: 1;
  line-color: #FFF;
  line-opacity: 0.5;
}
```

![choropleth](https://cloud.githubusercontent.com/assets/5215798/19595755/b6cf2e54-978b-11e6-8ee3-198d30677e3d.png)

<br>

<a name="brewer"></a>
#### Color Brewer

* Apply the following SQL query:

```sql
SELECT * FROM nyc_census_blocks

```

* Apply the following CartoCSS style (`CTR + S`):

```css
#layer { 
  line-width: 1; 
  line-color: #FFF; 
  line-opacity: 0.5; 
  polygon-fill: ramp([shape_area], colorbrewer(YlGn), quantiles(5)); 
  polygon-opacity: 0.7;
}
```

![brewer](https://cloud.githubusercontent.com/assets/5215798/19595760/c088e4a8-978b-11e6-88cb-d0fd75647218.png)

<br>

<a name="bivariate"></a>
##### Bivariate

* Apply the following SQL query:

```sql
SELECT * FROM railroad_data
```

* Apply the following CartoCSS style (`CTR + S`):

```css
#layer {
  marker-width: ramp([total_damage], range(1, 20), quantiles(5));
  marker-fill: ramp([railroad], (#5B3F95, #1D6996, #129C63, #73AF48, #EDAD08, #E17C05, #C94034, #BA0040, #8E1966, #6F3072, #CDD2D4), category(10));
  marker-opacity: 0.5;
  marker-fill-opacity: 1;
  marker-allow-overlap: true;
  marker-line-width: 1;
  marker-line-color: #FFF;
  marker-line-opacity: 0.4;
}
```

![bivariate](https://cloud.githubusercontent.com/assets/5215798/19595757/bcaa9c28-978b-11e6-9c16-1eb524dfa1df.png)

<a name="buildings"></a>
##### Buildings

* Enter this URL `http://builder-demo.carto.com/api/v1/map` as "Maps API endpoint".
* Apply the following SQL query:

```sql
SELECT 
  * 
FROM 
  bcn_buildings 
WHERE 
  sobre_rasa > 0
ORDER BY 
  ST_YMax(the_geom) DESC
```

* Apply the following CartoCSS style (`CTR + S`):

```css
#layer {
  line-width: 0.25;
  line-color: #FFF;
  line-opacity: 0.5;
  building-fill: ramp([sobre_rasa], (#ffffcc, #c7e9b4, #7fcdbb, #41b6c4, #1d91c0, #225ea8, #0c2c84), jenks);
  building-fill-opacity: 1;
  building-height: [sobre_rasa]*2;
}
```

![buildings](https://cloud.githubusercontent.com/assets/5215798/19849334/f301478a-9f52-11e6-8abd-5907aa979937.png)

<br>

<a name="opacity"></a>
##### Opacity

* Apply the following SQL query:

```sql
SELECT * FROM world_borders
```

* Apply the following CartoCSS style (`CTR + S`):

```css
@locationred: #F24440;

#layer {
 polygon-fill: @locationred;
  polygon-opacity: ramp([pop_norm], 0.2, 1), quantiles);
  line-width: 0.4;
  line-color: lighten(@locationred, 20);
  line-opacity: 0.25;
}
```

![opacity](https://cloud.githubusercontent.com/assets/5215798/20213445/0502b112-a809-11e6-86e9-9c5cf5abc8fe.png)

<a name="lines"></a>
##### Lines

* Apply the following SQL query:

```sql
SELECT * FROM county_commuter_flow
```

* Apply the following CartoCSS style (`CTR + S`):

```css
#layer {
  line-width: ramp([workers_in_flow], range(1, 3.5), quantiles(5));
  line-color: ramp([workers_in_flow], (#f6d2a9, #f3aa84, #ea8171, #d55d6a, #b13f64), quantiles);
  line-comp-op: darken;
}
```

![lines](https://cloud.githubusercontent.com/assets/5215798/20277610/8f96b3e2-aaa1-11e6-8e7f-02044b3924b8.png)

##### Cartocolors

* Apply the following SQL query:

```sql
SELECT * FROM ne_50m_admin_0_countries
```

* Apply the following CartoCSS style (`CTR + S`):

```css
#layer {
    polygon-opacity: 0.5;
    polygon-fill: ramp([mapcolor7], cartocolor(Bold), category(7));
    line-width: 2;
    line-color: ramp([mapcolor7], cartocolor(Bold), category(7));
    line-opacity: 0.4;
    line-offset: -1;
}
```

![cartocolors](https://cloud.githubusercontent.com/assets/5215798/20308086/b8d38284-ab42-11e6-95d8-452bb6cd7382.png)

<hr>

<a name="resources"></a>
### RESOURCES

* [Turbo Carto repository](https://github.com/CartoDB/turbo-carto)
* [CARTO basic viewer block](http://bit.ly/carto-viewer)
* [Introduction to Map Design](https://carto.com/academy/courses/design-for-beginners/)
* [Styling with Turbo Carto](https://carto.com/blog/styling-with-turbo-carto/)
* [CARTO color scale app](http://cartodb.github.io/labs-colorscales/)

<br>

# CARTO.js v3 ( deprecated version )

[CARTO.js v3](https://carto.com/docs/carto-engine/carto-js/) is the JavaScript library that allows to create web mapping apps using CARTO services quickly and efficiently. It's built upon the following components:

* [jQuery](http://jquery.com)
* [Underscore.js](http://underscorejs.org)
* [Backbone.js](http://backbonejs.org/)
* It can use either [Google Maps API](https://developers.google.com/maps/) or [Leaflet](http://leafletjs.com/)

The version explained of carto.js explained in this section is the carto.js v3.

There are several versions available and are defined in [this section](https://github.com/CartoDB/cartodb.js/blob/release-3.15.11/scripts/publish.js#L15-L24) of the CARTO.js code. Some of them are:

* CartoDB.js core: If you are not using Leaflet and you want to implement your own layer object. <script src="http://libs.cartocdn.com/cartodb.js/v3/3.15/cartodb.core.js"></script>. More information in [this section](https://carto.com/docs/carto-engine/carto-js/core-api) of CartoDB.js documentation.

* CartoDB.js without JQuery: If you don't want to load the big JQuery library. https://cartodb-libs.global.ssl.fastly.net/cartodb.js/v3/3.15/cartodb_nojquery.js

* CartoDB.js without Leaflet: If you don't want to use Leaflet.js with CartoDB.js https://cartodb-libs.global.ssl.fastly.net/cartodb.js/v3/3.15/cartodb_noleaflet.js

* HTTPS version of the CartoDB.js library. https://cartodb-libs.global.ssl.fastly.net/cartodb.js/v3/3.15/cartodb.js

Know more about CARTO.js on the [official documentation](https://carto.com/docs/carto-engine/carto-js/) and this [academy tutorial](https://carto.com/academy/courses/cartojs-ground-up/).

Next sections and an excerpt of the documentation with the most useful methods and a the end you'll find live examples of them that you can modify and hack in real time.

### Create Visualizations and Layers

#### `createVis()`

The most basic way to display your map from CARTO.js involves a call to:

```javascript
cartodb.createVis(div_id, viz_json_url)
```

Couched between the `<script> ... </script>` tags, `createVis` puts a map and CartoDB data layers into the DOM element you specify. In the snippet below we assume that `<div id='map'></div>` placed earlier in an HTML file.

```javascript
window.onload = function() {
  var vizjson = 'link from share panel';
  cartodb.createVis('map', vizjson);
}
```

And that’s it! All you need is that snippet of code, a script block that sources CARTO.js, and inclusion of the CARTO.js CSS file. It’s really one of the easiest ways to create a custom map on your webpage. `createVis` also accepts options that you specifiy outside of the CartoDB Editor. They take the form of a [JS object](http://www.w3schools.com/js/js_objects.asp), and can be passed as a third optional argument.

```javascript
var options = {
  center: [40.4000, -3.6833], // Madrid
  zoom: 7,
  scrollwheel: true
};

cartodb.createVis('map', vizjson, options);
```

### `createLayer()`

If you want to exercise more control over the layers and base map, `createLayer` may be the best option for you. You specifiy the base map yourself and load the layer from one or multiple viz.json files. Unlike `createVis`, `createLayer` needs a map object, such as one created by Google Maps or Leaflet. This difference allows for more control of the basemap for the JavaScript/HTML you’re writing.

A basic [Leaflet map](http://leafletjs.com/reference.html#map-class) without your data can be created as follows:

```javascript
window.onload = function() {
  // Choose center and zoom level
  var options = {
    center: [41.8369, -87.6847], // Chicago
    zoom: 7
  }

  // Instantiate map on specified DOM element
  var map = new L.Map(dom_id, options);

  // Add a basemap to the map object just created
  L.tileLayer('http://tile.stamen.com/toner/{z}/{x}/{y}.png', {
    attribution: 'Stamen'
  }).addTo(map);
}
```

The map we just created doesn’t have any CartoDB data layers yet. If you’re just adding a single layer, you can put your data on top of the basemap from above. If you want to add more, you just repeat the process. We’ll be doing much more with this later. This is the basic snippet to put your data on top of the map you just created. Drop this in below the `L.tileLayer` section.

```javascript
var vizjson = 'link from share panel';
cartodb.createLayer(map, vizjson).addTo(map);
```

#### Leaflet vs Google Maps in CARTO.js

By default, when you create a map with CartoDB.js you are also using [Leaflet.js](http://leafletjs.com/) library
  to create the map.

With Leaflet, the steps to create the map are:

1. Define map class object with [L.map](http://leafletjs.com/reference.html#map-class) method and its properties.
2. Add basemap object with [L.tileLayer](http://leafletjs.com/reference.html#tilelayer) method.
3. Add CartoDB layers with [createLayer](http://docs.cartodb.com/cartodb-platform/cartodb-js/api-methods/#cartodbcartodblayer) method.
4. Define Leaflet [event methods](http://leafletjs.com/reference.html#events)

However, with the Google Maps API, the ways to create the map are a bit different:

1. Define and add map class object and its options with [google.maps.Map](https://developers.google.com/maps/documentation/javascript/reference#Map)
2. Define Google Maps event methods with [google.maps.event.addListener](https://developers.google.com/maps/documentation/javascript/events#EventsOverview) method.
Example [here](http://bl.ocks.org/oriolbx/1fc7914e1887e102314a)

## Interactivity

When creating a map, you can activate the interactivity in order to show a popup when a user click or hover a geometry. When you activate the interactivity on a map, the UTF grid layer is loaded.

### What is UTF grid?

A UTF grid is a grid where each map tile has a compressed file of attributes registered to its pixel space. As tiles are loaded into the map, the attribute information is accessible after moving or clicking the mouse on the map/tile.

### UTF grid and CARTO

In CARTO, there is just one UTF grid for the whole CARTO layer.

 If you add more than one CARTO layer to a Leaflet or Google map, only the top layer will get the events. The tiles from the UTF grid of different layers are not mixed, CARTO.js gets all the UTF grids tiles separately and loads them into the map. For example, if you load 8 layers on your map, that means that you also are loading 8 UTF grids (one per layer).

### Interaction vs Interactivity

* **Interaction** (`setInteraction(true)`): the user input (= mouse event) is tracked/registered.
* **Interactivity** (`setInteractivity(columnName)`): get values of the geometry from a mouse event. The values come form the column or columns that have the interactivity enabled.


## Static Maps API with CARTO.js v3

Static views of CartoDB maps can be generated using the [Static Maps API](https://carto.com/docs/carto-engine/maps-api/static-maps-api) within CartoDB.js. The map’s style, including the zoom and bounding box, follows from what was set in the viz.json file, but you can change the zoom, center, and size of your image with a few lines of code.

In [this section](https://carto.com/docs/carto-engine/carto-js/static-maps) of our documentation you can find detailed information to use the Static Maps API with CARTO.js.


## UI Functions in CARTO.js v3

### Tooltips

A tooltip is an infowindow that appears when you hover your mouse over a map feature with vis.addOverlay(options). A tooltip appears where the mouse cursor is located on the map.

To add a tooltip to a map you need to do two steps:

First, define tooltip variable:

```javascript
  var tooltip = layer.leafletMap.viz.addOverlay({
            type: 'tooltip',
            layer: layer,
            template: '<div class="cartodb-tooltip-content-wrapper"><p>{% raw %}{{name}}{% endraw %}</p></div>',
            width: 200,
            position: 'bottom|right',
            fields: [{ name: 'name' }]
  });
```
Second, add tooltip to the map:

```javascript
     $('body').append(tooltip.render().el);
```

If the version of CARTO.js is lower than 3.14 (betweem 3.11 and 3.13), then the way to define a tooltip can be done this way:

```js
sublayer.set({ 'interactivity': ['cartodb_id', 'name'] }); // set interactivity

// define tooltip element
var i = new cdb.geo.ui.Tooltip({
    layer: layer,
    template: '<div class="cartodb-tooltip-content-wrapper"> <div class="cartodb-tooltip-content"><h3>{{name}}</h3><p>more info</p></div></div>',
    width: 200,
    position: 'bottom|right'
});

// add tooltip element to the map
$('body').append(i.render().el);
```

### Infobox

Similar to a tooltip, an infobox displays a small box when you hover your mouse over a map feature. When viewing an infobox on a map, the position of the infobox is fixed, and always appears in the same position.

To add a tooltip to a map you need to do two steps:

* Define infobox variable:

```js
var infoBox = layer.leafletMap.viz.addOverlay({
          type: 'infobox',
          layer: layer,
          template: '<div class="cartodb-tooltip-content-wrapper"><h3>Maximum Population</h3><p>{{pop_max}}<span> hab</span></p></div>',
          width: 150,
          position: 'top|right'
        });
```

* Add infobox element to map

```js
 $('body').append(infoBox.render().el);
```


### Infowindows

Infowindows provide additional interactivity for your published map, controlled by layer events. It enables interaction and overrides the layer interactivity. A pop-up information window appears when a viewer clicks on a map feature.

In order to add the CARTO.js infowindow you need to add this line within your code:

```javascript
cdb.vis.Vis.addInfowindow(map, layer, ['fields']);
```

However, you can create custom infowindows with different tools (`Moustache.js`, HTML or `underscore.js`). Whatever choice you make, you would need to create a template first and then add the infowindow with the template. Here we will see how to do it using `Moustache.js`.

[Mustache.js](http://mustache.github.io/) is a `logic-less` logic-template. That means that only tags you create templates that are replaced with a value or series of values, it works by expanding tags in a template using values provided in a hash or object.

Example: Custom infowindow template to display `cartodb_id`:

```html
  <script type="infowindow/html" id="infowindow_template">
    <div class="cartodb-popup v2">
  <a href="#close" class="cartodb-popup-close-button close">x</a>
  <div class="cartodb-popup-content-wrapper">
    <div class="cartodb-popup-content">
      <h4>ID</h4>
      <p>{% raw %}{{cartodb_id}}{% endraw %}</p>
    </div>
  </div>
  <div class="cartodb-popup-tip-container"></div>
</div>
</script>
```

Then you can apply the custom infowindow template to the map with:

```javascript
cdb.vis.Vis.addInfowindow(
          map, layer, [columnName],
          {
             infowindowTemplate: $('#infowindow_template').html()
          });
```


On the other hand, we can use [underscore.js](http://underscorejs.org/) to create infowindow. The underscore.js library allows use functions inside the infowindows (the functions must be global). An example of custom infowindow template could be this one, where the customTimeFunction() function is to get the current timestamp and display it inside the infowindow everytime that the geometry is clicked. In order to do that you would need to disable the sanitizeTemplate attribute of the infowindow when you create the HTML map. This is done by setting the value to false, disabling the infowindow security setting.

In this case, there is no need tu use Mustache tags to get the values from the dataset. By using <%= columnName %> you can get each value of the geometry.

```js
<!-- HTML template for custom infowindow -->
  <script type="infowindow/html" id="infowindow_template">
      <div class="cartodb-popup header blue v2">
        <a href="#close" class="cartodb-popup-close-button close">x</a>
         <div class="cartodb-popup-content-wrapper">

            <ul>
              <li><%= 'Country: '  + adm0name %></li>
              <li><%= 'City: ' + name %></li>
              <li><%= customTimeFunction() %></li>
            </ul>

            </div>
        </div>
        <div class="cartodb-popup-tip-container"></div>
      </div>
  </script>
```

```js
/* define function to be used inside Infowindow */
   function customTimeFunction(){
       var today = new Date();
       return ("You have clicked this point at: " + today.getUTCDay() + '/'+today.getUTCMonth() + '/'
       +today.getFullYear() + ' ' + today.getHours() +':'+ today.getMinutes() + ':' + today.getSeconds());
   }
```

Then, when you add the infowindow with underscore.js [_template](http://underscorejs.org/#template) function:

```js
cdb.vis.Vis.addInfowindow(
          map,
          layer,
          ['name','adm0name','pop_max'],
          {
            'sanitizeTemplate':false
          }).model.set({
            'template' :  function(object){
                    return _.template($('#infowindow_template').html())(object);
              }
            });        
        });
```

In [this example](http://bl.ocks.org/oriolbx/80216cfc465ffd6152c2) we have used the underscore.js to add a javascript function that is executed everytime that the infowindow is opened.


### Legends

In order to add legends with CARTO.js you would need to define the elements and colors of the legend with HTML, then you could use the legend classes of CARTO.js to create the legends.

There are two kind of legend classes:

First, `cartodb-legend choropleth`, applied in Choropleth maps:

```html
<div class='cartodb-legend choropleth'>
    <div class="legend-title">Population</div>
      <ul>
        <li class="min">
          1256
        </li>
        <li class="max">
          8300
        </li>
        <li class="graph count_441">
        <div class="colors">
        <div class="quartile" style="background-color:#FFFFB2"></div>
        <div class="quartile" style="background-color:#FED976"></div>
        <div class="quartile" style="background-color:#FEB24C"></div>
        <div class="quartile" style="background-color:#FD8D3C"></div>
        <div class="quartile" style="background-color:#FC4E2A"></div>
        <div class="quartile" style="background-color:#E31A1C"></div>
        <div class="quartile" style="background-color:#B10026"></div>
        </div>
        </li>
      </ul>
  </div>
```

Second, `cartodb-legend category`, applied in simple or category maps:

```html
    <div class='cartodb-legend category'>
      <div class="legend-title" style="color:#284a59">Countries</div>
        <ul>
          <li><div class="bullet" style="background-color:#fbb4ae"></div>Spain</li>
          <li><div class="bullet" style="background-color:#ccebc5"></div>Portugal</li>
          <li><div class="bullet" style="background-color:#b3cde3"></div>France</li>
        </ul>
      </div>
```

## Torque

In CARTO Builder or CartoDB.js, the formatter/slider of animated maps will show different information depending on the date range -- the technical details are available [here](https://github.com/CartoDB/cartodb.js/blob/develop/src/geo/ui/time_slider.js).

The details are as follows:
  * Range is less than 1 day: `time`
  * Range is less than 3 days: `day + time`
  * More than 3 days but less than a year: `month/day/year`
  * Range is more than a year: `month/year`
  
On the other hand, there are known issues for torque performance in Firefox browsers. The workarounds to solve those issue are:
  * Disable hardware acceleration in Firefox
  * Reduce the torque resolution.
  	***An accumulated torque at maximum resolution is the most CPU demanding set up***
  * Currently, CARTO.js v4 does not support Torque.

## Examples

Each example points to a real time viewer showcasing a different procedure using CARTO.js. Feel free to change and play with the code and refresh the webpage to return to the initial state.

* Cartodb.js for on-premises [gist](https://gist.github.com/ernesmb/af3f957d109d0686612d)
* Load a simple visualization [plnkr](http://plnkr.co/edit/plhwv3IQwFxLHBGWodQp?p=preview)
* Load an anonymous map [plnkr](http://plnkr.co/edit/aG7ZSN24WBE6d8ZJQTqY?p=preview)
* Load a named map [plnkr](http://plnkr.co/edit/HitBoz?p=preview)
* Change in SQL and CartoCSS
  * Anonymous map [plnkr](http://plnkr.co/edit/SzMbyj?p=preview)
  * Named map [plnkr](http://plnkr.co/edit/c7gFkI?p=preview)
* Execute a SQL query [plnkr](http://plnkr.co/edit/z3BpG1?p=preview)
* Interactivity
  * Events, feature click [plnkr](http://plnkr.co/edit/88Qc99jdgP4X43FSTRdH?p=preview)
  * Custom infowindows [plnkr](http://plnkr.co/edit/9FE6BHILGudsIrDmCzdd?p=preview)
  * Custom tooltips [plnkr](http://plnkr.co/edit/uD0Fdw?p=preview)

The following list presents a number of running examples on doing many different tasks.

* Layer selector: dropdown menu. [bl.ocks](http://bl.ocks.org/oriolbx/bbadc6aa0b5a8e133792)
* Layer selector: dropdown menu & Torque layers. [bl.ocks](http://bl.ocks.org/oriolbx/8aa36a091ce32ae939ab156c13b2aa10)
* CARTO.js & Leaflet.js: Change zoom level with custom zoom controls. [bl.ocks](http://bl.ocks.org/oriolbx/f9a75b1dc017e7a8af2a)
* SQL & CARTO.js: Download dataset with button. [bl.ocks](http://bl.ocks.org/oriolbx/0476fe891f5ab5e3615c2f3f16ec1d18)
* SQL & CARTO.js: Custom search box. [bl.ocks](http://bl.ocks.org/oriolbx/1e3755a44583058f4b95)
* CARTO.js & D3.js: 3D world minimap. [bl.ocks](http://bl.ocks.org/oriolbx/3f753e4e51e797bbbf27)
* Disable pan and zoom options. [bl.ocks](http://bl.ocks.org/oriolbx/937a83b2260350ccc267)
* Custom infowindow, custom infobox & custom tooltip. [bl.ocks](http://bl.ocks.org/oriolbx/3950e1a9b458a9177f9c)
* Custom infowindow with youtube video inside. [bl.ocks](http://bl.ocks.org/michellechandra/0890a67f513f3fe674db)
* Classification methods: Using layer selector + SQL API to classify your data with different options. [bl.ocks](http://bl.ocks.org/oriolbx/8525229e34263adcaeaf)
* CARTO.js & JQuery UI: Filtering time data with a slider. [bl.ocks](http://bl.ocks.org/iriberri/7335eebcdda69b0b4f80)
* SQL & CARTO.js: Adding points from map to dataset. [bl.ocks](http://bl.ocks.org/ernesmb/beb25f539f8ff38bbd891e6d114ea7f4)
* Complete Named map example. [bl.ocks](http://bl.ocks.org/ernesmb/f5c7bffe033e898bf9f2)
* Some advance examples
  * Highlight polygons on hover with Leaflet + `sql.execute` [bl.ocks](http://bl.ocks.org/javisantana/8313604)
  * Custom layer selector [bl.ocks](http://bl.ocks.org/jsanz/6a83dbae9d6e984ca938)
  * Aggregate content in SQL clusters [bl.ocks](http://bl.ocks.org/ernesmb/348b9eed9ee4c7038fd7)
  * Get feature properties on Torque time [bl.ocks](http://bl.ocks.org/ernesmb/4939b3751d3be0cdd64b)
  * Dynamic graph using Chart.js & CARTO.js [bl.ocks](http://bl.ocks.org/ernesmb/4939b3751d3be0cdd64b)
  * Create graph using CSS and mustache template: [bl.ocks](http://bl.ocks.org/jsanz/1bd917d87f4f606fe778)
  * Google API graph [GitHub code](https://github.com/CartoDB/cartodb.js/blob/develop/examples/infowindow_with_graph.html) and its working [here](http://cartodb.github.io/cartodb.js/examples/infowindow_with_graph.html)

# Webmapping apps with CARTO.js <a name="cartojs"></a>

## CARTO.js

[CARTO.js](https://carto.com/docs/carto-engine/carto-js/) is the JavaScript library that allows to create web mapping apps using CARTO services quickly and efficiently. It's built upon the following components:

* [jQuery](http://jquery.com)
* [Underscore.js](http://underscorejs.org)
* [Backbone.js](http://backbonejs.org/)
* It can use either [Google Maps API](https://developers.google.com/maps/) or [Leaflet](http://leafletjs.com/)

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

## UI Functions

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
# Leaflet.js

<!-- MarkdownTOC -->

- Intro to LeafletJS
- Getting started
- Adding a GeoJSON layer
- Styling a GeoJSON layer
- Adding a Pop-up
- Filtering the layer
- Events
- Play with a live example

<!-- /MarkdownTOC -->

## Intro to LeafletJS

## Getting started

Creating a basic map with Leaflet is actually pretty easy, this Javascript snippet will create a map with a single marker and a popup:

```javascript
var map = L.map('map').setView([51.505, -0.09], 13);

L.tileLayer('http://{s}.tile.osm.org/{z}/{x}/{y}.png', {
    attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors'
}).addTo(map);

L.marker([51.5, -0.09]).addTo(map)
    .bindPopup('A pretty CSS3 popup.<br> Easily customizable.')
    .openPopup();
```

Let's see what each part does:

### [Map object](http://leafletjs.com/reference-1.0.3.html#map)

```javascript
var map = L.map('map').setView([51.505, -0.09], 13);
```

There are three different `map` on that single line! let's elaborate a bit: 

* `var map`: This is the variable that will hold the map object
* `L.map`: This makes reference to the [Leaflet map class](http://leafletjs.com/reference-1.0.3.html#map)
* `'map'`: This is the HTML DOM object where we'll render the map

This line uses Leaflet's Map class for creating a new `map` object on the DOM element identified by `'map'`. 

The `setView` method is necessary in order to center the map to an specific location and zoom level. 

### [Tiled layer](http://leafletjs.com/reference-1.0.3.html#tilelayer)

Most tiled web maps follow certain Google Maps conventions:

* Tiles are 256x256 pixels
* At the outer most zoom level, 0, the entire world can be rendered in a single map tile.
* Each zoom level doubles in both dimensions, so a single tile is replaced by 4 tiles when zooming in. This means that about 22 zoom levels are sufficient for most practical purposes.
* The Web Mercator projection is used, with latitude limits of around 85 degrees.
* The _de facto_ OpenStreetMap standard, known as Slippy Map Tilenames or XYZ follows these and adds more:
  * An X and Y numbering scheme
  * PNG images for tiles
  * Images are served through a REST API, with a URL like `http(s)://.../Z/X/Y.png`, where Z is the zoom level, and X and Y identify the tile.

  (from [Wikipedia: Tiled web map](https://en.wikipedia.org/wiki/Tiled_web_map))


> Check [this link](https://www.mapbox.com/help/how-web-maps-work/) to know more about tiles and how they work

This is the code that let us add a tiled layer to a leaflet map: 

```javascript
L.tileLayer('http://{s}.tile.osm.org/{z}/{x}/{y}.png', {
    attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors'
}).addTo(map);
```

* Remember to always add the attribution
* `.addTo()` is a method of the `tileLayer` class (and many others) that adds the declared layer to the map object. It expects the variable with the map object as a parameter. 

> Check [this tiled layers catalog](http://leaflet-extras.github.io/leaflet-providers/preview/) to find more basemap options.

### [Marker](http://leafletjs.com/reference-1.0.3.html#marker)


Adding a single marker to a Leaflet map is as easy as: 

```javascript
L.marker([51.5, -0.09]).addTo(map)
    .bindPopup('A pretty CSS3 popup.<br> Easily customizable.')
    .openPopup();
```

This adds a marker as a new layer to the map. It expects at least an array with pair of **LatLon** coordinates. 

`.bindPopup()` adds a popup with custom HTML content inside. `.openPopup()` opens the popup programmatically. 

##### Check a live version of this basic example [here](http://plnkr.co/edit/cDszbYcgUZexjCPzoSmT?p=preview). Feel free to play with it!

## Adding a [GeoJSON](http://leafletjs.com/reference-1.0.3.html#geojson) layer

Another possibility for adding data to a Leaflet map would be loading a set of geometries (and their properties) using a geoJSON file. 
We're going to use this [Spanish cities dataset](../src/populated_places.js). 

First thing we need to do is storing the geoJSON features in a variable (on `populated_places.js` file) and load the file on the HTML's `<head>` section:

```html
<script src="populated_places.js"></script>
```

After that, adding the features as a vector layer in Leaflet is quite easy: 

```javascript
L.geoJSON(populatedPlaces, {}).addTo(map);  
```

Note that we're just referencing the variable name, and not passing any other option to `geoJSON()` method. We'll come back to that later. 

This approach has a very important drawback. Vector layers in Leaflet use a lot of memory on the browser, which leads to slow map performance depending on the computer and the amount of features to be loaded. 

This really wouldn't work for a map with thousands of features, but is a nice and easy approach for a map in the range of a few hundreds features. 



## Styling a GeoJSON layer

Let's dig a bit more on how to work with geoJSON layers, with some cartographic enhancements. 

Leaflet uses functions that return certain values or objects in order to set layer options. For example:

* `pointToLayer` option defines the way in which each point geometry is going to be represented on the map. By default it returns a `L.marker` object, but we can set different options for different symbolizers, such as: 

    ```javascript
    pointToLayer: function(feature,latlng){
        return L.circleMarker(latlng)
    }
    ```

    Note the function expects two arguments: 

    * `feature` makes reference to each point feature on the layer.
    * `latlng` is a `L.latLng` object that contains the geographic position of the feature. 

    Those arguments could be used inside the function to play with them as you want. They could be called however you like, but bear in mind the first one will always make reference to the feature, and the second to the `L.latLng` object. 

* `style` option is used to set the styling rules for the layer. It only expects a `feature` argument

    ```javascript
    style: function(feature){
        return {
            "fillColor":"#F00",
            "fillOpacity":0.6,
            "stroke":false,
            "radius":feature.properties.pop_max/100000
        }
    }
    ```

    In this case, the function returns an object with a key/value configuration. It applies the same style to every feature, but we could also add some conditional structure to create a choropleth map. 

    Note that we can use the `feature` variable inside the function to make reference to the feature's properties and use them to set style rules. 

* There are other options that add functionality to the layer, we'll see some of them later but check [this link](http://leafletjs.com/reference-1.0.3.html#geojson) for the complete reference.  

## Adding a Pop-up

We could add a simple, static Popup that shows the same content for every feature in the map, using the `bindPopup()` method we saw before. 

However, in order to create dynamic Popups that show information stored as feature properties, we need a different approach. 

We'll do so by passing another option to `L.geoJSON` layer declaration: 

```javascript
onEachFeature: function(feature,layer){
    layer.bindPopup(
        '<h2>'+feature.properties.name+'</h2>'+
        '<b>Population:</b> '+feature.properties.pop_max
        )
}
```

This will render the HTML code passed as argument to the function, but is also able to get values from the feature properties. 


## Filtering the layer

We could also apply a filter based on each feature properties, the `filter` options iterate over the geojson layer and applies the function we have defined to return `true` or `false` depending on whether a feature should be rendered or not. 

```javascript
filter: function(feature){
    if (feature.properties.pop_max < 100000){
        return false
    }
    else {
        return true
    }
}
```

The filter above will only show cities with more than 100K inhabitants. 


## [Events](http://leafletjs.com/reference-1.0.3.html#map-event)

Registering an event allows to perform certain actions based on changes on the map state, user interaction, etc

We can easily register an event and set the subsequent actions like this: 

```javascript
map.on('moveend', function(){
    coords.innerHTML='<b> CENTER: </b>'+map.getCenter()
})
.on('zoomend', function(){
    zoom.innerHTML='<b>ZOOM: </b>'+map.getZoom()
});
```

In this case, we're updating the HTML content of `coords` and `zoom` DOM elements after the map position or zoom level changes. 

We could also listen to layer events: 

```javascript
.on('click', function(e){
    map.panTo(e.latlng)
    coords.innerHTML=e.latlng
});
```

In this case, we make use of the event object (referenced with `e`), in order to use one of its properties (`e.latlng`)


## [Play with a live example](http://plnkr.co/edit/N453u2wW6jSNCHks37OY?p=preview)




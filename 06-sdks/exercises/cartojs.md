# Webmapping apps with CARTO.js <a name="cartojs"></a>

[CARTO.js v4](https://carto.com/developers/carto-js/) is the Javascript library that allows to create location intelligence apps using CARTO services quickly and efficiently.

The main difference with the previous CARTO.js version is that CARTO.js v4 is a low level library which provides the methods needed to build the maps and use the dataviews, which are the models of data used in the widgets of CARTO Builder. This allows the users to create their own custom popups and widgets with their own code or using an external library wothout worrying about incompatibility problems.

Also, CARTO.js v4 does not include the google maps or Leaflet library in the code, allowing the usage of any 1.x version of Leaflet.js or Google Maps.

## CARTO Auth API and CARTO.js v4

With the new CARTO Auth API, we provide more flexibility to work with the datasets of the account in order to great great location intelligence apps. 

In this section of CARTO Developer Center, you can find detailed information about how the [authorization works](https://carto.com/developers/fundamentals/authorization/) in CARTO and in this one how to use the [CARTO Auth API](https://carto.com/developers/auth-api/).

CARTO.js v4 does not support Named Maps, so in order to create public maps from private data, it is mandatory to create a regular API Key type with Maps permission, so CARTO datasets can be used on a pubic map,but the data will be kept private.

## Usage of CARTO.js v4

Here you can find the [CARTO.js Quickstart guide](https://carto.com/developers/carto-js/guides/quickstart/) which shows how to build a map with the CARTO.js library.

If you have used the previous version of CARTO.js (version 3.x), you will notice that the structure to create the maps have change a little bit, in case that you want to check the differences between the previous and the current version, we would recommend checking [this section](https://carto.com/developers/carto-js/guides/upgrade-considerations/) of the CARTO Developer Center.

In order to learn more about the different methods that CARTO.js provides, we would recommend taking a look at [the CARTO.js reference](https://carto.com/developers/carto-js/reference/). 

## Dataviews

Dataviews are a way to extract data from CARTO in predefined ways (eg: a list of categories, the result of a formula operation, etc.) and they can be used to create custom widgets in our apps that will be static or that will change depending on the bounding box of the map.

Dataviews are the model of data that will be used to create the different widgets of your location intelligence apps. Take a look at [the CARTO.js reference](https://carto.com/developers/carto-js/reference/) in order to learn more about the different options of CARTO.js dataviews. 

### Tile aggregation

With CARTO.js v4, we can set an aggregation of our data, so we can display less data depending on the zoom level.
In [this blog post of CARTO](https://carto.com/blog/inside/tile-aggregation/) and in [this section of the CARTO Maps API](https://carto.com/developers/maps-api/guides/tile-aggregation/) you can find detailed information about how the tile aggregation works in CARTO.

In [this section](https://carto.com/developers/carto-js/reference/#cartolayeraggregation) of the CARTO.js reference you can find detailed information about using tile aggregation in CARTO.js

## Examples

Each example points to a real time viewer showcasing a different procedure using CARTO.js. Feel free to change and play with the code and refresh the webpage to return to the initial state.

* Load a simple visualization [plnkr](https://plnkr.co/edit/xSs8ehEf5TygjQdU9DiL?preview)
* Load a simple visualization with tile aggregation [plnkr](https://plnkr.co/edit/bAuMdlJxwuou9DebyiWF?p=preview&preview)
* Interactivity
  * Events, feature click and hover [plnkr](https://plnkr.co/edit/QonoHnPYFtj6KWQPd1Ux?p=preview&preview)
* Dataviews
  * Category dataview [plnkr](https://plnkr.co/edit/2WbA6jlLhcrphn40nYcV?preview)


You can find much more examples with the code in the [CARTO Developer center](https://carto.com/developers/carto-js/examples/) 

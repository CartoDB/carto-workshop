# Zaragoza Bikes

* *Degree of Difficulty*: ***
* *Goal*: Getting started with CARTO BUILDER.
* *Features Highlighted*:
  * Import: connect datasets from links
  * Analysis: Create areas of influence, Intersect with second layer, Enrich with Data Observatory.
  * Cartography: auto-style.
* *Datasests needed*:
  - Zaragoza blocks: [https://builder-demo.carto.com/api/v2/sql?q=SELECT+*+FROM+manzana&format=shp&filename=manzanas](https://builder-demo.carto.com/api/v2/sql?q=SELECT+*+FROM+manzana&format=shp&filename=manzanas), the original data source can be download it from [CartoCiudad](http://datos.gob.es/es/catalogo/e00125901-cartociudad).
  - Public bikes anchors: [http://www.zaragoza.es/api/recurso/urbanismo-infraestructuras/estacion-bicicleta.geojson?rf=html&results_only=false&srsname=wgs84&rows=129](http://www.zaragoza.es/api/recurso/urbanismo-infraestructuras/estacion-bicicleta.geojson?rf=html&results_only=false&srsname=wgs84&rows=129), you can check what the API call returns copying [this url](http://www.zaragoza.es/api/recurso/urbanismo-infraestructuras/estacion-bicicleta?rf=html&results_only=false&srsname=wgs84&rows=129) into your browser.

## Contents

- [Create a map](#map)
- [Analysis 1: Create areas of influence from bikes anchors](#aoi)
- [Analysis 2: Intersect areas and city blocks](#intersect)
- [Analysis 3: Assign total population to city blocks](#do)
- [Widgets and auto-styling](#widgets)
- [Publish](#publish)

<hr>

## Create a map <a name="map"></a>

* Click on **`NEW MAP`**.
* Click on **`CONNECT DATASET`**. Submit this url, choose any `Sync my data` option if sync table feature is available for your account, and make sure the `Let CARTO automatically guess data types and content on import` box is checked: [http://www.zaragoza.es/api/recurso/urbanismo-infraestructuras/estacion-bicicleta.geojson?rf=html&results_only=false&srsname=wgs84&rows=129](http://www.zaragoza.es/api/recurso/urbanismo-infraestructuras/estacion-bicicleta.geojson?rf=html&results_only=false&srsname=wgs84&rows=129). Click **`CONNECT DATASET`** button.
* Click on **`CREATE MAP`**.
* From the map's **`LAYERS`** pane click **`ADD`**. Click **`CONNECT DATASET`**. Connect the `Zaragoza blocks` dataset using the same steps as above, with this url: [https://builder-demo.carto.com/api/v2/sql?q=SELECT+*+FROM+manzana&format=shp&filename=manzanas)
* Double-click on the map layer titles to rename them "Bike anchors" and "Blocks". You can also rename the map this way.

![map](imgs/zgzbikes/01-layers.png)
<figcaption>A view of BUILDER main dashboard. Our layer order might be different than yours depending on which file you imported first.</figcaption>


## Create areas of influence from bikes anchors <a name="aoi"></a>

## Intersect areas and city blocks <a name="intersect"></a>

## Assign total population to city blocks <a name="do"></a>

## Widgets and auto-styling <a name="widgets"></a>

Consider adding the following widgets:

* formula widget for the sum of parking spots
* formula widget for the average of parking spots per point
* histogram for the parking spots
* category for the parking types
* category for the original neighborhoods dataset
* category for the aggregated neighborhoods dataset, so you can order by number of parking slots

## Publish and share options <a name="publish"></a>

* Publish the map
  * Click on **`SHARE`**.
  * Set privacy to `LINK` or `PUBLIC`.
  * Click on **`PUBLISH`**.
  * Now you can share the map as a [link](https://team.carto.com/u/builder-demo/builder/608968d1-3353-4184-833d-e1c67428beff/embed) or embed it.


```html
<iframe width="100%" height="520" frameborder="0"
  src="https://team.carto.com/u/builder-demo/builder/608968d1-3353-4184-833d-e1c67428beff/embed"
  allowfullscreen webkitallowfullscreen mozallowfullscreen
  oallowfullscreen msallowfullscreen>
</iframe>
```


![map](imgs/bikes/07-result.png)
<figcaption>Final result</figcaption>

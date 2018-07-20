# MAPS API GUIDE

## Contents
<!-- MarkdownTOC -->

- Introduction
- Anonymous maps
- Named maps
- Static maps
- Custom API endpoints

<!-- /MarkdownTOC -->


## Introduction
### General concepts
The Maps API allows to create maps based upon SQL queries and CartoCSS rules. A certain combination of selected data and a set of CartoCSS rules produces a CartoDB layer, which can contain one or more sublayers. One or more layers produce a layergroup, which is assigned a temporal identifier called `layergroupid`.

Using the API we can instantiate maps via POST request, and expect a layergroupid as part of the response. This layergroupid is thereby used to perform further API calls to retrieve map tiles. The URL for each tile contains a ZXY-based reference.

The layergroupid is not persistent, so any request made using an expired layergroupid will return a 400 error from the server. A new layergroupid is generated when any change is performed to the map. The current limit for `layergroupid` persistency is **two hours**.

> Check **[this example](http://bl.ocks.org/ernesmb/raw/e5bf4a0826bb4fa85830/)**, where a map is instantiated, the layergroupid retrieved and a tile requested.

The example above shows how we can use the Maps API to instantiate a map using SQL and CartoCSS code. As a result, a layergroupid is generated and used to request the tiles. 

The layergroupid is used afterwards in the example to form the URL to request a tile.

```javascript
var tileURL = 'https://USERNAME.cartodb.com/api/v1/map/'+data.layergroupid+'/0/0/0.png'
```

## Anonymous maps
### Instantiation: Cartodb.js calls to the Maps API
Let's see how cartodb.js construct a call to the Maps API based on the LayerSource object passed to the library. 

This is the cartodb.createLayer() code: 
```javascript
  layerSource={
    user_name: 'USERNAME',
    type: 'cartodb',
    sublayers: [{
       sql: 'SELECT * FROM table_name',
       cartocss: '#layer {polygon-fill: #F00;}'
    }]
  }

  map= new L.Map('map', {
    zoom:1,
    center:[0,0]
  });

  cartodb.createLayer(map, layerSource,{
    	//options
  }).addTo(map)
  .done(function(layer){
        //do stuff
  });
```
When executed, the following call is sent to the Maps API to instantiate the map
```bash
curl 'http://USERNAME.cartodb.com/api/v1/map
  ?stat_tag=API
  &config={
  	"version":"1.3.0",
  	"stat_tag":"API",
  	"layers":[
  		{"type":"cartodb",
  			"options":{
  				"sql":"SELECT * FROM table_name",
  				"cartocss":"#layer {polygon-fill: #F00;}",
  				"cartocss_version":"2.1.0"
  			}
  		}
  	]
  }
  &callback=_cdbc_1655719025_1'
```

Which produces a response with many details on how to get raster and vector tiles. Note that it also contains the layergroupid:

After that, tiles are requested with simple calls of the type:
```bash
curl 'https://cartocdn-gusc-a.global.ssl.fastly.net/USERNAME/api/v1/map/b356a24187b9390c94d08ed134ab5fe9:1451998092397/0/1/0/0.png' --compressed
```

We could use the layergroupid to get some more information, as the `grid.json` file that is used to define layer interactivity (check the link in the **[example](http://bl.ocks.org/ernesmb/raw/e5bf4a0826bb4fa85830/)**).

Also, we are able to select which layers are going to be drawn in the tiles, passing a specific layer as parameter, several of them or only one. The layer parameter is the number before the ZXY reference.

## Named maps
### Definition
Named maps are different from what is described above. This kind of map is based upon *private data*, so queries coming from unauthenticated users (which would be virtually any client loading the map) could not retrieve the data. 

In order to allow any client to access the underlying data without being authenticated, the map configuration is stored in the server and the map is given an specific name, by which it will be referenced afterwards. 

### Instantiation
First thing is creating a `config.json` file that contains the map configuration parameters: 

```json
{
  "version": "0.0.1",
  "name": "world_borders",
  "auth": {
    "method": "token",
    "valid_tokens": [
      "mexicolindo"
    ]
  },
  "placeholders": {
    "color": {
      "type": "css_color",
      "default": "red"
    },
    "population_filter": {
      "type": "number",
      "default": 1
    }

  },
  "layergroup": {
    "version": "1.0.1",
    "layers": [
      {
        "type": "cartodb",
        "options": {
          "cartocss_version": "2.1.1",
          "cartocss": "#layer { marker-fill: <%= color %>; }",
          "sql": "select * from world_borders WHERE pop2005 >= <%= population_filter %>",
          "interactivity" : ["cartodb_id","name","pop2005"]
        }
      }
    ]
  },
  "view": {
    "zoom": 3,
    "center": {
      "lng": 0,
      "lat": 36
    }
  }
}
```

We have defined two placeholders that will allow to change the color of the map, and filter each country by the population. 

The `curl` command to upload and instantiate the map is the following: 
```bash
curl -X POST -H 'Content-Type: application/json' -d @config.json 'https://USERNAME.cartodb.com/api/v1/map/named?api_key=API_KEY'
```
In case we need to update the `config.json` file, we could use: 

```bash
curl -X PUT -H 'Content-Type: application/json' -d @config.json 'https://USERNAME.cartodb.com/api/v1/map/named/world_borders?api_key=API_KEY'
```

The API key must always be used to upload and instantiate the map. 

> Check the **map** and the **cartodb.js** code **[here](http://bl.ocks.org/ernesmb/1d1b2fc6894a25e52331)**

We can alter the parameters defined as placeholders to query the data while it is still kept private in the CartoDB's account database. 

Each time we alter those parameters, a new layergroupid is received and thereby used to fetch the tiles.

The CartoDB Editor uses placeholders for toggling sublayers in named maps. 

It does so using a `<%=layerN%>` placeholder that is used in the SQL layer definition to create an easy condition that may return true or false by just changing a simple parameter. For example:

We have this in the `template.json` file: 
```json
"placeholders": {
    "layer0": {
      "type": "number",
      "default": "1"
    }
```
also, 
```json
"sql": "SELECT * FROM (SELECT * FROM world_borders) AS wrapped_query WHERE <%= layer0 %>=1"
```
The resultant query would be: 
```sql
SELECT * FROM (SELECT * FROM world_borders) AS wrapped_query WHERE 1=1
```
As the `WHERE` clause will return `true` for every row, the layer will be drawn to the map. 

However, if we changed the `<%= layer0 =>` parameter to 0, the resultant query would be
```sql
SELECT * FROM (SELECT * FROM world_borders) AS wrapped_query WHERE 0=1
```
That condintion will return `false` for every row, so the sublayer will not be shown.


### XYZ tiles
We can use any namedmap to fetch the tiles as we would do with a basemap. This allows to create complex multi-layered maps and load it as an http tile layer. 

For this purpose, we will follow the same logic we use with the layergroupid, but using the map name instead:

  - We are able to request an specific tile from our named map:    
    - [http://USERNAME.cartodb.com/api/v1/map/named/world_borders/all/0/0/0.png](http://ernestomb.cartodb.com/api/v1/map/named/world_borders/all/0/0/0.png?auth_token=ernesto)
    
    ![](http://ernestomb.cartodb.com/api/v1/map/named/world_borders/all/0/0/0.png?auth_token=ernesto)

  - Also, we can pass the configuration parameters as part of the URL, instantiating the map with the passed configuration. We are going to instantiate the map using "orange" as the `color` parameter:    
    - [http://USERNAME.cartodb.com/api/v1/map/named/world_borders/all/0/0/0.png?config={"color":"orange"}](http://ernestomb.cartodb.com/api/v1/map/named/world_borders/all/0/0/0.png?config={"color":"orange"}&auth_token=ernesto)
    
    ![](http://ernestomb.cartodb.com/api/v1/map/named/world_borders/all/0/0/0.png?config={%22color%22:%20%22orange%22}&auth_token=ernesto)

  - We could also apply a filter passing the `population_filter` parameter:
    - [http://USERNAME.cartodb.com/api/v1/map/named/world_borders/all/0/0/0.png?config={"color":"orange","population_filter":40000000}](http://ernestomb.cartodb.com/api/v1/map/named/world_borders/all/0/0/0.png?config={"color":"orange","population_filter":40000000}&auth_token=ernesto)
    
    ![](http://ernestomb.cartodb.com/api/v1/map/named/world_borders/all/0/0/0.png?config={%22color%22:%20%22orange%22,%20%22population_filter%22:40000000}&auth_token=ernesto)

  - As the named map has been configured to have an authentication token, we need to pass it as an URL parameter: 
    - [http://USERNAME.cartodb.com/api/v1/map/named/world_borders/all/0/0/0.png?auth_token=mexicolindo](http://ernestomb.cartodb.com/api/v1/map/named/world_borders/all/0/0/0.png?auth_token=ernesto)

  - We could also select which tile we want to retrieve, passing the z, x, y parameters in the request:
    - [http://USERNAME.cartodb.com/api/v1/map/named/world_borders/all/5/15/12.png?auth_token=mexicolindo](http://ernestomb.cartodb.com/api/v1/map/named/world_borders/all/5/15/12.png?auth_token=ernesto)
    
    ![](http://ernestomb.cartodb.com/api/v1/map/named/world_borders/all/5/15/12.png?auth_token=ernesto)

  - Finally we could also use the cdn to retrieve tiles: 
    - [https://cartocdn-gusc-a.global.ssl.fastly.net//USERNAME/api/v1/map/named/world_borders/all/0/0/0.png?config={"color":"green"}](https://cartocdn-gusc-a.global.ssl.fastly.net//ernestomb/api/v1/map/named/world_borders/all/0/0/0.png?config={"color":"green"}&auth_token=ernesto)

    ![](https://cartocdn-gusc-a.global.ssl.fastly.net//ernestomb/api/v1/map/named/world_borders/all/0/0/0.png?config={%22color%22:%20%22green%22}&auth_token=ernesto)

  - As we said before, we could use these URLs as basemaps. Check [this CartoDB map](https://team.cartodb.com/u/ernestomb/viz/a5efedd0-f515-11e5-b6d6-0e31c9be1b51/embed_map) that uses the following URL for the basemap: 
    
    ```
    http://USERNAME.cartodb.com/api/v1/map/named/world_borders/all/{z}/{x}/{y}.png?config={"color":"orange"}&auth_token=mexicolindo
    ```

#### Retina tiles
  - @2x retina tile notation is not technically supported for Named Maps, but you can get them this way:
    - instantiate a named map using the template_id returned after you uploaded your config.json:
    ```
    curl -X POST -H 'Content-Type: application/json' -d @config.json 'https://USERNAME.cartodb.com/api/v1/map/named/world_borders?auth_token=API_KEY'
    ```
    - Use that response's layergroup id and @2x notation:
    ```
    http://USERNAME.cartodb.com/api/v1/map/your_layergroup_id/0/0/0@2x.png
    ```
  - You can use the same retina request for Anonymous Maps, just use the layergroup id returned when you upload the config.json.
  
    ![retina_screenshot](https://cloud.githubusercontent.com/assets/1779444/16201563/ff02fb18-36df-11e6-895a-054bd5f005b6.png)

## Static maps
The Static Maps API is able to generate images from instantiated maps, based on parameters passed in the URL and the layergroupid we get in the instantiation response

### Instantiation
The instantiation is made in the same way as anonymous maps, so we need a SQL query and a set of CartoCSS rules. 
> Again, use this **[example](http://bl.ocks.org/ernesmb/raw/e5bf4a0826bb4fa85830/)** to instantiate the map and get the layergroupid we need to request static maps. Scroll down a bit to see the Static Map generator

### Getting images
We can use several parameters to define the extension and resolution of an static map generated via Static Maps API: 
 
  - **Center + Zoom:** We could specify the latitude/longitude coordinates of the map center and the zoom level. The image resolution and the format will also be passed as parameters: 
  ```
  GET /api/v1/map/static/center/{layergroupid}/{z}/{lat}/{lng}/{width}/{height}.{format}
  ```

  - **BoundingBox:** Alternatively, we could set the bounding box of the map, and the desired resolution: 
  ```
  GET /api/v1/map/static/bbox/{layergroupid}/{bbox}/{width}/{height}.{format}
  ```

  - The bounding box should be passed in this order, comma-separated:
    1. Most Western
    2. Most Southern
    3. Most Eastern
    4. Most Northern

  so the endpoint could also be defined as: 

    ```
    GET /api/v1/map/static/bbox/{layergroupid}/{west},{south},{east},{north}/{width}/{height}.{format}
    ```
  - **Named map:** A named map could also produce a static image just by passing the image resolution as parameters. It will take the center from the view defined in the `config.json` file
    ```
    GET /api/v1/map/static/named/{name}/{width}/{height}.{format}
    ```
    - [http://USERNAME.cartodb.com/api/v1/map/static/named/world_borders/600/400.png?auth_token=mexicolindo](http://ernestomb.cartodb.com/api/v1/map/static/named/world_borders/600/400.png?auth_token=ernesto)

    ![](http://ernestomb.cartodb.com/api/v1/map/static/named/world_borders/600/400.png?auth_token=ernesto)

Other layers could be added to the static map, they just need to be declared in the mapConfig object we use for the instantiation. 

## Custom API endpoints
Cartodb.js automatically send the map instantiation and tiles requests to the Map API endpoint in our cloud infrastructre. 
If we were using an Onpremise instance of CartoDB, we would need the client to send the requests for tiles and map instantiation to a different endpoint, one that matches the Map API in the Onpremise installation.

When using a `viz.json` file as layerSource object, the URL to the file will automatically be composed in the onpremise Editor. However, if we were using the `createLayer()` method, the following structure is required: 

```javascript
cartodb.createLayer(map, {
    user_name: 'USERNAME',
    maps_api_template: "https://onpremise.domain.address:443/user/{user}",
    sql_api_template: "https://onpremise.domain.address:443/user/{user}",
    tiler_protocol: "http",
    tiler_domain: "onpremise.domain.address",
    tiler_port: "80",
    filter: "mapnik",
    type: 'cartodb',
    sublayers: [{
      sql: 'SELECT * FROM table',
      cartocss: '#layer{marker-width:8; marker-fill:#ff6600}',
      interactivity: 'cartodb_id'
    }
```

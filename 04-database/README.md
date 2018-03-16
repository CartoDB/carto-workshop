Geospatial Databases
=====================

## Agenda

* SQL and Postgres
* PostGIS: most important functions, data types, etc
* Stored procedures and triggers creation

## Resources

* [Introduction to  SQL](https://docs.google.com/a/cartodb.com/presentation/d/1LRa6HHdtUCrxl7Kh4wjgTktkigoihVekuhwH-dW5jv8/edit?usp=sharing)
* [Easy exercises with SQL](exercises/sql-easy.md)
* [Spatial SQL](exercises/sql-spatial.md)
* [Advanced Postgres & PostGIS workshop](exercises/sql-advanced.md)
* [Stored procedures and triggers examples](exercises/sql_stored_procedures.md)

Other useful links:

* [PostGIS Project](http://postgis.net/) and [official docs](http://postgis.net/docs/manual-2.2/)
* [WKT encoding](https://en.wikipedia.org/wiki/Well-known_text) 
* [Modern SQL](http://modern-sql.com/slides) 
* [Use the Index, Luke](http://use-the-index-luke.com)
* [The 10 most powerful SQL queries](https://www.youtube.com/watch?v=ZLvT8lQit80) 
* [Pattern matching expressions](https://www.postgresql.org/docs/9.5/static/functions-matching.html)
* [Fill the gaps between two geometries](http://bl.ocks.org/jsanz/60050dbfe104da69f15e)
* [Join by proximity](http://bl.ocks.org/jsanz/5f7ac01cc6c720c71610c74917d821aa)
* [Jump across the Date Line](https://carto.com/blog/jets-and-datelines)
* EPSG codes databases:
  * http://epsg.io/
  * http://spatialreference.org/
* [Free your maps from Web Mercator](https://carto.com/blog/free-your-maps-web-mercator/)
* [Stored procedures that return the Antipode's point for an input geometry](https://github.com/geoinquietosvlc/antipodes-map/blob/master/pgsql/funciones.sql)

Extra ball: 

* **Cartodbfy a table**. CARTO needs some special columns, indexes, etc in order to work with tables and show them on the interface.
 For this, there is a function that is executed upon import, but that you may need to call manually if you created a table with a `CREATE TABLE` query: 

```
CREATE TABLE wb2 AS
SELECT w.*,
       count(pp.*) AS places,
       sum(pp.pop_max)  AS cities_pop
  FROM world_borders AS w
  JOIN ne_10m_populated_places_simple AS pp
    ON ST_Intersects(w.the_geom,pp.the_geom)
 GROUP BY w.cartodb_id
 ORDER BY cities_pop DESC
 LIMIT 10;
``` 

And then, 

```
SELECT CDB_CartoDBFyTable('username','wb2');
``` 

Note that your `username` is needed as first parameter if you're an organization user. 

Día 3
-----------------------------

* http://www.naturalearthdata.com/
* http://www.w3schools.com/cssref/css_colors.asp
* http://cartodb.github.io/labs-colorscales/
* https://github.com/mapbox/maki
* https://cartoworkshops.carto.com/u/cdmx18/builder/f47506e6-bc95-11e6-9ccb-0e233c30368f

Consulta para calcular cortes:

```sql
WITH data as (SELECT
  *,
  pop2005/area as pop_norm
FROM
  world_borders
WHERE
  area > 0
)
select unnest(CDB_QuantileBins(array_agg(pop_norm),4)) from data
```

Más en https://carto.com/docs/tips-and-tricks/carto-functions/#statistical-functions

Más sobre selectores:

* https://carto.com/docs/carto-engine/cartocss/properties/
* https://tilemill-project.github.io/tilemill/docs/guides/selectors/


Código para mejorar etiquetas cortadas

```css
Map{
  buffer-size: 512;
}
```

Torque avanzado: http://andrewxhill.com/blog/2015/04/17/torque-unknown/

Altura tipo "edificio"

```css
#layer {
  /* global */
  polygon-opacity: 0.7;
  line-width: 1;
  line-color: black;
  line-opacity: 1;
  
  building-fill:#fcde9c; 
  building-fill-opacity: 1;
  building-height: [pop2005]/ 2500 ;

/* categories */
  [ pop_norm > 190.0 ] {
    building-fill: #f58670;
  }
  [ pop_norm > 650 ] {
    building-fill: #e34f6f;
  }
  [ pop_norm > 1401 ] {
    building-fill: #d72d7c;
  }
  [ pop_norm > 4500 ] {
    building-fill: #7c1d6f;
  }

}
```

CARTO y Cesium:

* https://cesium.cartodb.io/
* https://github.com/CartoDB/labs-cesiumjs


Resultado mapa Africa

* Jorge: https://cartoworkshops.carto.com/u/carto-workshops/builder/f5ebdb96-bcc5-11e6-93f7-0e05a8b3e3d7/embed
* Omar: https://cartoworkshops.carto.com/u/cdmx07/builder/1ef46896-bcc6-11e6-a444-0ee66e2c9693/embed
* Fernando: https://cartoworkshops.carto.com/u/cdmx15/builder/d76cd840-853b-4728-9100-d4df1602d33a/embed 
* Mario: https://cartoworkshops.carto.com/u/cdmx08/builder/feef73ce-bcc5-11e6-bc32-0ecd1babdde5/embed
* Ramón: https://cartoworkshops.carto.com/u/cdmx02/builder/fd374eda-bcc5-11e6-9ac8-0e3ff518bd15/embed


Día 4
----------------------------

Crear tabla desde consulta y cartodbficar

```sql
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
 
 SELECT CDB_CartoDBFyTable('miusuario','wb2');
 ```
 
* http://postgis.net/
* http://postgis.net/docs/manual-2.2/
* https://en.wikipedia.org/wiki/Well-known_text
 
Paginas para los codiigos de las proyecciones

* http://epsg.io/3857
* http://spatialreference.org/
* https://carto.com/blog/free-your-maps-web-mercator/

Otros:

* http://modern-sql.com/slides
* https://www.youtube.com/watch?v=ZLvT8lQit80
* http://bboxfinder.com
 

Truco para el corte de  NY a Tokyo: https://carto.com/blog/jets-and-datelines/
 
Procedimiento almacenado: https://github.com/geoinquietosvlc/antipodes-map/blob/master/pgsql/funciones.sql
 
```sql
CREATE OR REPLACE FUNCTION insertpoint(
        lon numeric,
        lat numeric,
        tablename text
  )
RETURNS TABLE(cartodb_id INT)
LANGUAGE 'plpgsql' SECURITY DEFINER
RETURNS NULL ON NULL INPUT AS $function$
DECLARE
  sql text;
BEGIN
sql = 'WITH do_insert AS ( INSERT INTO ' ||quote_ident(tablename)|| '(the_geom) VALUES '||'('||'ST_SetSRID(ST_MakePoint('||lon||','||lat||'), 4326)'||')'||'RETURNING cartodb_id)'||'SELECT cartodb_id FROM do_insert';
RETURN QUERY EXECUTE sql;
END;
$function$
```

Ejemplo de SQL espacial: http://bl.ocks.org/jsanz/60050dbfe104da69f15e

Encoder/Decoder direcciones web: http://meyerweb.com/eric/tools/dencoder/

Día 5
-----------

* https://github.com/CartoDB/mobile-android-samples

Infowindow por función: 

* http://bl.ocks.org/jsanz/a89ba37800b9b280146c
* http://bl.ocks.org/jsanz/raw/d6831b16303567c2af07/

SQL: obtener las geometrías más cercanas dada otra:
* http://bl.ocks.org/jsanz/5f7ac01cc6c720c71610c74917d821aa


SQL: https://www.postgresql.org/docs/9.3/static/functions-matching.html

* https://github.com/CartoDB/carto-python/tree/1.0.0
* https://cartodb.github.io/training/



